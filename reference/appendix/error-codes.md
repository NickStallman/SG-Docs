# Error and result codes

Every numeric status, error and result code the WiNet dongles and the devices behind them
report, collected in one place and grouped by subsystem. Codes are decimal unless written as
hex (`0x…`). Where a code originates in a page of its own, that page is linked.

## Web UI / local API result codes

Every HTTP and WebSocket reply carries a `result_code`. `1` means success; `-1` and `0` carry
the text the handler chose. Any other number is looked up in a fixed 61-entry table and
turned into a message; a code **not** in that table is reported as a generic "internal
abnormal". The web server returns HTTP 200 for application errors, so clients must read
`result_code`, not the HTTP status.

### The lookup table (61 entries)

| Code | Meaning | Code | Meaning |
|---:|---|---:|---|
| 0 | set successfully | 318 | CRC check error |
| 100 | maximum number of users reached | 319 | other error |
| 101 | another user is logged in | 320 | no update available |
| 102 | wrong account or password | 321 | open failed |
| 103 | session timed out — logged out | 322 | package length error |
| 104 | another login in progress | 323 | connection failed |
| 106 | account session stale | 325–330 | communication busy: update / search / IV curve / transmit / YMODEM / port |
| 107 | firmware update error | 331 | client-count limit reached |
| 114 | account locked | 332 | protocol not supported |
| 115 | too many wrong-password attempts | 333 | device-count limit reached |
| 116 | R&D (developer) permission not enabled | 334 | client connection failed |
| 118 | certificate expired | 350 | network-configuration issue |
| 119 | access not allowed | 351 | station rebuild required |
| 218 | file error | 352 | public-key version missing or mismatched |
| 219 | PLC network retrieval issue | 353 | digital-signature check failed |
| 220 | time check out of range | 391 | set failed |
| 221 | app-locked user | 392 | precondition for emergency setting not met |
| 301 | read failed | 393 | device port occupied |
| 304 | port error | 431 | read holding-registers error |
| 305 | device-count limit reached | 432 | read input-registers error |
| 306 | address conflict | 434 | write single-register error |
| 311 | malformed configuration template | 444 | write multiple-registers error |
| 312 | duplicate detected | 450 | blocked (shielded) registers error |
| 313 | response error | 451 | read/write register error |
| 314 | port busy | 4104 | upgrade check error |
| 315 | open failed | 4106 | another upload in progress |
| 316 | package format error | 4107 | cloud update in progress |
| 317 | software version mismatch | 4109 | uploading a lower version |

### Codes from the access layer and common handlers

These are returned directly by the session/parameter layer and are **not** in the table
above, so most surface to the user as "internal abnormal":

| Code | Meaning |
|---|---|
| 1 | success |
| 110 | privilege too low |
| 120 | token unknown |
| 200 | token parameter missing |
| 201 | missing or invalid parameter |
| 206 | request could not be queued |
| 209 | no answer from the device/module |
| 212 | invalid method (not a POST) |
| 213 | directory could not be created |
| 217 | upgrade not allowed / no free buffer |

The dead "add device by type over the network" branch (there is no such feature on either
dongle; only RS-485 ports can be scanned) answers "add over Ethernet not supported" and the
web layer shows **301** ("read failed"). The HTTP register-passthrough path turns a device
port-state value of 1–4 into its own codes 301–304.

## Dongle self-update codes

Codes the dongle reports when updating **itself**. See
[../firmware-update/update-channels.md](../firmware-update/update-channels.md) and
[../firmware-update/sgu-container.md](../firmware-update/sgu-container.md).

| Code | Meaning |
|---|---|
| 316 | package format error (bad container magic or structure) |
| 318 | CRC / MD5 integrity check failed |
| 320 | no update available |
| 322 | package length error |
| 352 | public-key version does not match the stored key |
| 353 | detached-signature check failed |
| 4104 | upgrade check error — on the WiNet-S2, an unsigned package at or above the version floor |
| 4105 | system upgrade error — after reboot the running version is not the expected one |
| 4106 | another upload already in progress |
| 4107 | a cloud update is in progress |
| 4109 | the package version is lower than the running version |

## Cloud upgrade codes

### `upgradeStatus` (10308 per-device, per-file)

A four-hex-digit code: `1xxx` success, `2xxx` failure, `3xxx` in progress.

| Code | Meaning |
|---|---|
| 3000 | waiting |
| 3008 | file downloading |
| 3009 | file downloaded; device being programmed |
| 1004 | file installed on this device |
| 2003 | install failed |
| 2011 | package rejected, or self-upgrade left the wrong version running |
| 2103 | download failed |
| 2110 | download timed out |
| 2112 | target device offline |
| 2116 | a combined `.sgz` file was offered for the dongle itself |

### `exceptCode` module bytes

Per-module exception bytes that may accompany a failure. The meanings are the candidates
suggested by the web interface's module-error map and are not confirmed:

| Byte | Meaning |
|---|---|
| 100 | device timeout |
| 101 | IAP v2 not supported |
| 102 | not upgrading |
| 103 | status-query timeout |
| 104 | missing packets |
| 105 | single packet abnormal |
| 106 | all packets abnormal |
| 107 | DSP number inconsistent |

### Cloud command result handling

- A **10376** reply is acted on only when its `data.result_code` is `"1"`; anything else is
  logged and dropped. The value is read as a string.
- The **10308** progress message's top-level `result` field is always `"1"`; per-device
  outcome is carried by `upgradeStatus`, not by this field.
- In the LAN-pull channel, the server's reply to the dongle's `result` POST must set
  `result_code` to the JSON **string** `"1"`; a JSON number crashes the dongle before it
  flashes. See [../firmware-update/update-channels.md](../firmware-update/update-channels.md).

## Modbus exception usage

On RS-485 and over Modbus-TCP the dongle and devices use the standard Modbus exception
codes; the one that matters for firmware relay is exception `0x04` (slave device failure),
which the inverter returns for a rejected bootloader-mailbox write so the dongle resends.

When the local API or the register-passthrough layer relays a Modbus error to a client, it
maps it to one of the web result codes above: 431 (read holding registers), 432 (read input
registers), 434 (write single register), 444 (write multiple registers), 450 (blocked
register), 451 (read/write). The ad-hoc parameter read/write path answers a device write
exception with 391 and a bad read (or a zero register address) with 301.

## Device bootloader (inverter / battery) codes

Reported by the inverter during a firmware relay; full context in
[../firmware-update/device-firmware-relay.md](../firmware-update/device-firmware-relay.md).

**Stage** (register 1-based 20005): 1 idle/closed, 2 session open, 3 failed, 4 success,
7 sub-module phase done.

**Error-flag bits** (register 1-based 20006):

| Bit value | Meaning |
|---|---|
| `0x02` | login token mismatch |
| `0x04` | session inactivity timed out |
| `0x08` | IAP sequence or version mismatch (relay) |
| `0x10` | relay target not accepted |
| `0x20` | module name mismatch |
| `0x40` | version not newer |
| `0x200` | staged-image CRC-32 mismatch |
| `0x400` | downstream module errored or timed out during a relay |
| `0x4000` | trailer CRC or RSA signature check failed |

**Dongle-side fault codes** for a relay job: 100 (handshake or timeout), `0x65` (wrong IAP
version), `0x66` (not in update stage after login), `0x67` (status or job timeout), `0x68`
(lost-packet report out of range), `0x69` (same packet lost five times), `0x6A` (too many
lost-report rounds), `0x6B` (module-count mismatch).
