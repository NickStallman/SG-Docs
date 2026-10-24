# The dongle update container (`.sgu`)

A WiNet dongle updates its own firmware from a single container file with the extension
`.sgu`. The same container format and the same verification code run on both the WiNet-S
(ESP32, application family `WINET-SV200`) and the WiNet-S2 (ESP32-S3, application family
`WINET-SV300`); only the baked-in version strings and one signature rule (see
[The WiNet-S2 signature rule](#the-winet-s2-signature-rule)) differ between them.

A `.sgu` holds the dongle application image and the web-UI filesystem image, a fixed-size
header, and — on signed packages — a signature trailer. Nothing in it is encrypted: both
payloads are stored in clear. Integrity is covered by an MD5 field; authenticity by
RSA-2048 signatures that are verified against a public key held on the dongle.

How a `.sgu` reaches the dongle (a LAN pull, a cloud push, or a browser upload) and how it
is flashed are in [update-channels.md](update-channels.md). How the dongle, in turn,
updates the inverter and battery is in [device-firmware-relay.md](device-firmware-relay.md).

## Byte layout

All multi-byte scalars are little-endian. `total_len` is the u32 at offset `0xA0`.

| Offset | Size | Field | Contents |
|---|---|---|---|
| `0x00` | 32 | MD5 checksum | 32 ASCII hex characters: the MD5 of everything from `0x20` to the end of the file (the trailer, if present, is included) |
| `0x20` | 64 | Application version | NUL-padded ASCII, e.g. `WINET-SV300.001.03.Pnnn` |
| `0x60` | 64 | Bootloader version | NUL-padded ASCII, e.g. `WINET-SV300.001.03.Bnnn` |
| `0xA0` | 4 | Body length (`total_len`) | Total length in bytes of the part list at `0xA4` |
| `0xA4` | `total_len` | Part list | A sequence of typed parts (below) that fills `[0xA4, 0xA4 + total_len)` exactly |
| `0xA4 + total_len` | — | Signature trailer | Present only on signed packages (below) |

### The part list (TLV body)

The body is a list of parts, each `{u16 type, u32 length, data[length]}`. The dongle walks
the list part by part, bounded by `total_len`, so the trailer is never mistaken for a part.

| Type | Meaning | Written to |
|---|---|---|
| 2 | ESP application image (esptool format, begins `0xE9`) | the inactive OTA application slot, through the platform's standard OTA writer |
| 3 | Raw filesystem image | the web-UI LittleFS partition (erased, then written) |
| other | any other part type is rejected | — |

At least one non-empty type-2 or type-3 part is required; empty parts are skipped. A shipped
package is exactly two parts in this order: `[type 2 application][type 3 filesystem]`. The
type-3 image is the size of the web-UI partition; a type-3 part whose size does not match
causes the filesystem driver to reject the block count.

### The signature trailer

A signed package appends the following after the body. The trailer is **not** covered by
`total_len` and is **not** part of the hash the signature is computed over (below), but it
**is** covered by the MD5 field at `0x00`.

| Offset in trailer | Size | Field | Value |
|---|---|---|---|
| `0x00` | 2 | Magic | `0x0200` |
| `0x02` | 4 | Trailer total | `signature_length + key_length + extra_length + 0xC` |
| `0x06` | 4 | Signature length | `0x100` (256 — RSA-2048) |
| `0x0A` | 4 | Public-key length | Length of the embedded PEM key, e.g. `0x1C3` (451); may be `0` |
| `0x0E` | 4 | Extra length | `0` |
| `0x12` | 256 | Signature | RSA-2048 signature over the body (below) |
| `0x112` | `key_length` | Public key | PEM-encoded RSA-2048 public key (SubjectPublicKeyInfo); absent when `key_length` is `0` |

An unsigned package (older WiNet-S packages below the version floor) has no trailer: the
file ends at the last byte of the body.

## Verification

Before a `.sgu` is flashed the dongle runs three checks. All must pass.

### 1. MD5 integrity

The 32 ASCII-hex characters at `0x00` are compared against a freshly computed MD5 of the
file from `0x20` to end of file. This is an integrity check only; it uses no secret.

### 2. Self-check against the embedded key

If the trailer carries a public key (`key_length` is non-zero), the dongle verifies that the
trailer's signature was produced by the trailer's own key. The signed quantity is formed as
follows:

1. Compute SHA-256 over the file region `[0x20, 0x20 + total_len + 0x84)` — that is, from the
   application-version string through the end of the last body part. (The constant `0x84`
   folds in the two 64-byte version fields and the 4-byte length field between `0x20` and
   the body at `0xA4`.)
2. Render the 32-byte digest as **64 lowercase ASCII hex characters**.
3. Verify the 256-byte trailer signature over those 64 ASCII bytes with **RSA-2048,
   PKCS#1 v1.5, no DigestInfo wrapping** (the hex string is itself the signed message).

This check only proves the package is internally consistent: the key that signed it travels
with it. It does not establish trust.

### 3. Authenticity against the on-device key

The package ships with a **detached** 256-byte signature file (see
[Distribution](#distribution)). The dongle verifies that detached signature over the file,
with the same RSA-2048 PKCS#1 v1.5 scheme, against the **public key stored on the dongle**
(below). This is the only check that establishes authenticity, and it is the check an
attacker cannot pass without the matching private key or without replacing the stored key.

### Version floors

Each dongle generation carries a baked-in current version and a degrade floor. A package
whose version is at or below the running version is refused unless a "allow downgrade"
switch has been set. The WiNet-S2 additionally refuses a package at or above its floor if
the package is unsigned (see below).

### No encryption

The application image and the filesystem image are stored in clear inside the container.
There is no cipher anywhere in this path; the only cryptography is the MD5 integrity field
and the RSA-2048 signatures.

## The trust store

The on-device public key lives on the external flash as two files: the key itself
(`publicKey.pem`) and a 10-byte version label (`publicKey.ver`). On first use, if no key is
present, it is provisioned from the read-only OEM partition, where the factory places a
`publicKey*.pem` file. The key's version label comes from the file name; the package's
`Version_signature.xml` names the matching key file as `publicKey_V<n>.pem`, and the dongle
parses the `_V<n>` label from that name.

The stored key can be replaced through the local web API, and the cloud-push and
LAN-pull channels replace it with the key that travels with the package before verifying
(see [update-channels.md](update-channels.md)). The authenticity check in
[step 3](#3-authenticity-against-the-on-device-key) is only as strong as the channel that
delivered the key now in the trust store.

## Distribution

A dongle update is distributed as a `.zip` containing three members:

| Member | Purpose |
|---|---|
| `<name>.sgu` | the container described above |
| `<name>.bin` | the detached 256-byte RSA-2048 signature verified in step 3 |
| `Version_signature.xml` | names the public-key file (`type="publickey"`) and thus the key version label |

The browser-side updater unpacks the zip and submits the members to the dongle one at a
time. The `Version_signature.xml` is read only to derive the public-key version label; it is
not itself uploaded to the dongle.

## The WiNet-S2 signature rule

The WiNet-S2 enforces a rule the WiNet-S does not. When an incoming package declares a
version **at or above** the S2's baked-in floor, a signature trailer is **mandatory**: a
package without a trailer is refused with result code **4104** ("upgrade check error") and
never flashed. A package whose declared version is **below** the floor is allowed on the
legacy unsigned path, with the trailer skipped.

The WiNet-S has neither the floor string nor the check: its body walk runs to end of file,
so on the S a trailer, if present, is simply read as one more part ending at EOF, and an
unsigned package is accepted at any version (subject to the degrade floor).

Because an OTA only ever rewrites the application slot and the web-UI filesystem — never the
second-stage bootloader or the partition table (see [flash-layout.md](../hardware/flash-layout.md))
— the update path cannot change the generation's floor or the way the bootloader validates
an image. The signature rule is therefore fixed for the life of the device from the
update's point of view.
