# Updating the inverter and battery (device relay)

When the cloud (or an installer) tells the dongle to update a connected device, the dongle
plays the device's firmware over RS-485, one device at a time, into a reserved register
window the device firmware treats as a bootloader mailbox. The inverter is always the
RS-485 endpoint: images for the inverter's own ARM and DSP, for the AFCI, for the string
inverter's optimizers (MLPE), and for the SBR battery control unit all arrive on the same
mailbox, and the inverter forwards the non-ARM images onward itself.

This is a write-heavy sequence with real risk: a wrong or truncated image can leave a device
unbootable. The transport and format are documented here for completeness.

Addresses on this page are **1-based**, as in Sungrow's published Modbus documentation
(subtract 1 for the value on the wire). Function codes are given in hex.

## The Modbus mailbox transport

The transfer is ordinary Modbus RTU to the device's own slave address: function `0x10`
(write multiple registers) to send, functions `0x03`/`0x04` (read) to read status. No other
function code is used. The device interprets a reserved register range as a bootloader
channel rather than as data registers.

Two wire protocols exist:

| Protocol | Register window (1-based) | Status |
|---|---|---|
| **V2** | 20000–20175 | the only one the SH10RS and SG5.0RS implement |
| **V3** ("IAP 3.0") | 20052–20100 | a second block that no firmware in these systems answers |

The dongle picks the protocol from the file type, not by probing the device. A plain `.sgu`
is sent as V2; a combined `SungrowIAP3.0` file would be sent as V3.

### V2 register map (inverter side)

| Address (1-based) | Dir | Meaning |
|---|---|---|
| 20000 | read | IAP version; `01 00 02 00` identifies V2 |
| 20003 | read | module count |
| 20004 | read | capability word (bit 0 = "accepts encrypted packets") |
| 20005 | read | stage: 1 idle/closed, 2 session open, 3 failed, 4 success, 7 sub-module phase done |
| 20006 | read | error-flag bits (see [failures](#what-a-failed-relay-looks-like)) |
| 20010–20014 | write | login block: `0xAA` opens a session (or `0x55` closes it), followed by a fixed 8-byte token |
| 20015 | write | per-session inactivity timeout seed |
| 20019 | write | hold flag — while non-zero, data packets are ignored |
| 20022–20036 | read/write | the 30-byte module string (e.g. `SUNSTONE-H_01011.02.69`) |
| 20037 | write | tag word (controls the name and "not newer" checks) |
| 20039–20040 | write | image CRC-32 (u32, low word first) |
| 20041–20042 | write | image length in bytes (u32, low word first) |
| 20043 | write | packet size |
| 20044 | write | packet count |
| 20045 … | write | data packets (below) |
| 20171 | read | first packet not yet received (1-based; 0 = none) |
| 20172–20173 | read | byte offset of that packet |
| 20174 | read | size of that packet |
| 20175 | read | number of packets still missing |

32-bit register values are **low word first**. A data packet carries, in order: a u16
packet number (1-based), a 4-byte target offset, a u16 payload length, then the payload
bytes. The register count of each write is `4 + length / 2`.

### The V2 sequence

For one device the dongle, under the RS-485 COM lock (all other devices on that bus are
paused):

1. **Raise the bus to 115200 baud** by writing the device's temporary-baud registers
   (1-based 21004 and 21006), then switching its own UART. The device reverts to 9600 after
   a period of silence, so the whole job runs at the high rate.
2. **Read the bootloader (IAP) version** (20000) and confirm V2.
3. **Read the status block** (20003 onward): module count, capability, stage, error flags.
4. **Log in**: write `0xAA` plus a fixed 8-byte token at 20010 to open the session. (The
   token value is not reproduced here.) A wrong token sets the error stage.
5. **Write the module descriptor** (20022 onward): the 30-byte module string, the tag word,
   the image CRC-32, the image length, the packet size and the packet count.
6. **Send the image in numbered packets** at the data window (20045). Packets are **208
   bytes** ("small") or **1 KB** ("big"); the big size is used only when every device on the
   bus advertises the capability for it. Each payload is **AES-128-ECB encrypted with a
   fixed embedded key** (the key is not reproduced here). The image is padded to a multiple
   of 16 bytes.
7. **Read the lost-packet report** (20171 onward) and **resend** any packet the device lists,
   until nothing is missing.
8. **Poll the status** (20005) until it reports success (stage 4) or failure (stage 3).
9. **Drop the bus back to 9600 baud** and release the COM.

```mermaid
sequenceDiagram
    participant C as Cloud
    participant D as Dongle
    participant I as Inverter ARM
    participant M as DSP / AFCI / MLPE / BCU
    C->>D: upgrade job + staged .sgu
    Note over D: verify SungrowIAP header + per-module CRC-32
    D->>I: raise bus to 115200, read IAP version + status
    D->>I: login (0xAA + 8-byte token) @ 20010
    D->>I: write module descriptor @ 20022
    loop each packet (208 B or 1 KB, AES-128-ECB)
        D->>I: data packet @ 20045
        I-->>D: echo
    end
    D->>I: read lost report @ 20171; resend as needed
    Note over I: after last packet: CRC-32 + secure-boot signature check
    I->>M: relay image over CAN / serial, or self-apply + reset
    D->>I: poll status @ 20005 (stage 4 = success)
    D->>C: progress / result (upgradeStatus codes)
```

## The device package format

### "IAP 2.0" — one module per `.sgu`

A single-module device image is a `.sgu` with this shape:

| Field | Size | Contents |
|---|---|---|
| Magic | 16 | `Sungrow\0\r\nIAP\0\0\0\0\0\r\n` |
| Version | ~8 | `2.0\0\r\n` |
| Header | 64 (total) | plain header carrying the body length and a CRC-32 (big-endian) |
| Body | n × 16 | AES-128-ECB over whole 16-byte blocks; any tail shorter than 16 bytes is left plain |

The decrypted body is a text descriptor followed by the raw code image:

| Key | Meaning |
|---|---|
| `[DEV] MOD=` | module / marketing name (e.g. `SAPPHIRE-H`, `AFCI-S`, `MLPE-CSU`, `BCU`) |
| `DVER=` / `HVER=` | descriptor software / hardware version |
| `[PCBn] …` / `[MCUn] CHIP=` | board and target-MCU identification |
| `APP=` | the firmware version string the device stores (e.g. `SUNSTONE-H_01011.02.69`) |
| `CRC=` | CRC-32 of the code image |
| `LEN=` | code length in bytes |
| `CODE=` | marks the start of the raw image |

### "IAP 3.0" — a combined multi-module container

When several modules are packed into one file for a staged update, the container begins with
the literal marker `SungrowIAP3.0` and holds a header per module: a module type, A/B slot
lengths, per-slot CRC-32 and version, a per-module payload length / CRC / offset, and a
whole-file CRC. Each module carries a version gate so a module whose version is same-or-lower
than what the device already runs is skipped.

### Module order and the battery package

The package manifest fixes the order modules are flashed, non-ARM modules before the ARM:

| System | Order |
|---|---|
| SH10RS hybrid | DSP, AFCI, ARM |
| SG5.0RS string | DSP, AFCI, MLPE-CSU, MLPE-MPLC, ARM |
| SBR battery control unit | a single package |

## Integrity and authenticity

The dongle itself checks only the **CRC-32** declared in the package — it performs no
signature check on a device image. Authenticity is enforced at the device.

Each module image ends with a **432-byte secure-boot trailer**:

| Offset in trailer | Size | Field | Value |
|---|---|---|---|
| 0 | 32 | name | `SungrowSecureBoot` |
| 32 | 4 | total trailer length | 432 |
| 36 | 4 | trailer checksum | CRC-32 (zlib) over the trailer's version and signature-length fields and the signature |
| 40 | 4 | version | 1 |
| 44 | 4 | signature length | 384 (RSA-3072) |
| 48 | 384 | signature | RSA-3072 PKCS#1 v1.5, SHA-256, over all image bytes before the trailer |

After the last packet, the **inverter's own ARM controller** verifies this trailer against a
public key embedded in its application. A bad or missing signature is fatal only while an
enforcement flag is set — a byte set by a holding-register write to 1-based 36332 (which
accepts only "enable" or "disable"). When enforcement is off, an unsigned image is accepted.
Its power-on state on the inverter is not yet confirmed.

## Where the image goes, and the relay

The inverter does not apply a device image in place while packets arrive. It stages the whole
image in a reserved window of its external SPI flash, decrypting each packet on arrival, and
records progress in non-volatile tags so a mid-transfer power cycle keeps what has landed.
Only after the last packet does the controller run the CRC-32 compare and the signature
check.

What happens next depends on the module:

- **The inverter's own ARM image** is applied by the inverter writing an "apply" request to
  a non-volatile tag and resetting itself; the device's own bootloader copies the staged
  image into internal flash on the next boot.
- **DSP and AFCI images** (module codes 03 and 06) are relayed by the inverter, acting as a
  second IAP master, over **CAN** to the DSP (slave address 1) and AFCI (slave address 6),
  using the same register protocol and the same login token.
- **Optimizer (MLPE) images** (module codes 32, 38, 39, 40) are relayed over the inverter's
  **serial link** at 115200 baud to the MLPE head module (not to individual optimizers).
- **The battery control unit image** (module code 22, SBR protocol) is relayed over **CAN
  identifier `0x1F0`** to the BCU. The dongle never addresses the battery directly; the
  inverter is the BCU's IAP master.

The relay sends the image without the 432-byte trailer (the inverter cuts the length back to
the trailer start after verifying it). The inverter stops its own conversion during a DSP,
optimizer or (SH10RS) Sungrow-battery session. Before starting a battery update the WiNet-S2 checks the inverter (it must
not be running, and total PV power must be below 500 W) and otherwise does not begin the relay.
The WiNet-S performs no such check.

## What a failed relay looks like

The inverter reports failure by setting stage 3 and an error-flag word at 1-based 20006:

| Bit value | Meaning |
|---|---|
| `0x02` | login token mismatch |
| `0x04` | session inactivity timed out |
| `0x08` | IAP sequence or version mismatch seen by the relay |
| `0x10` | relay target not accepted |
| `0x20` | module name does not match any of the inverter's modules |
| `0x40` | version not newer (when the "not newer" check is enabled) |
| `0x200` | staged-image CRC-32 differs from the declared value |
| `0x400` | a downstream module errored or timed out during a relay |
| `0x4000` | trailer CRC or RSA signature check failed |

The dongle maps its own view of the job to a fault code: 100 (handshake or timeout), `0x65`
(wrong IAP version), `0x66` (not in update stage after login), `0x67` (status or job
timeout), `0x68` (lost-packet report out of range), `0x69` (same packet lost five times),
`0x6A` (too many lost-report rounds), `0x6B` (module-count mismatch). These surface to the
cloud as the `upgradeStatus` failure codes and `exceptCode` bytes in
[update-channels.md](update-channels.md).

The dongle resends lost packets but gives up when the same packet is lost five times, when
the total lost reaches half the packet count, when a lost report is out of range, or when the
status read keeps timing out — so a relay that cannot complete ends as a reported failure
rather than hanging.
