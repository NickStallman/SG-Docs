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

## Inverter fault codes (SH10RS)

The inverter's power stage keeps its faults and alarms as bits in a set of 32-bit status words,
which the controller reads over the internal link and serves at 13050–13059 (five of the words).
The numeric codes in the active-fault list (5448–5472) and in the event log are those bits plus a
fixed offset per word, so a code identifies one bit:

| Status word (register) | Codes for bits 0–15 | Codes for bits 16–31 |
|---|---|---|
| 13052–13053 | bit + 2 (2–17) | bit − 16 + 100 (100–115) |
| 13058–13059 | bit + 19 (19–34) | bit − 16 + 200 (200–215) |
| 13054–13055 | bit + 300 (300–331) | |
| 13056–13057 | bit + 36 (36–51) | logged only, 52–67 |
| 13050–13051 | bit + 70 (70–85) | bit − 16 + 500 (500–515) |
| internal words not published | 133–148, 600–631, 700–731, 900–931 | |
| arc-fault word | bit + 87: bit 1 = **88** | |
| frequency-derating flag | **997** (listed as an alarm) | |
| battery words (from the battery management unit) | 732–763, 832–863, 932–963, 964–995 | |

Conditions that raise the first-level codes, read from the power-stage protection logic (the
thresholds are the installer registers named):

| Code | Condition |
|---|---|
| 2 | Grid overvoltage: any of the five voltage levels (32318–32322 with their delays) tripped |
| 3 | Grid transient over-voltage: the instantaneous grid voltage above its limit |
| 4 | Grid undervoltage: any of the five undervoltage levels tripped |
| 8 | Grid overfrequency: any overfrequency level tripped |
| 9 | Grid underfrequency: any underfrequency level tripped |
| 10 | Grid power outage: the anti-islanding trip, or the grid voltage below the islanding level for 6 s; forced while the grid voltage is under 30 V |
| 11 | DC injection: the DC component of the grid current above the 32282 threshold for the 32283 time, grid-tied, after 60 s of running |
| 12 | Excess leakage current: a sudden step of 70 mA or the residual-current evaluation above the 30403 threshold (enabled by 30405) |
| 13 | Grid abnormal: voltage or frequency outside the connection window (32536, 32537, 32549, 32550) for the hold time while waiting to connect |
| 14 | 10-minute mean grid overvoltage |
| 20 | DC bus overvoltage: bus above 600 V or a half-bus above 300 V |
| 24, 25 | DC bus capacitor unbalance, averaged and instantaneous |
| 39 | Low insulation resistance (ISO check during start-up) |
| 36, 37, 43 | Temperature-class channels beyond their thresholds for 1.6 s |
| 38 | Relay fault: the grid or backup relay did not close during start-up |
| 41 | Leakage-current sensor fault: the sensor self-test failed twice in standby or its offset is out of range |
| 44 | Inverter open-loop self-check failed: the bridge test step of the start-up sequence timed out three times |
| 47 | PV input configuration: the two PV inputs differ while 30207 declares them parallel |
| 48, 315, 316, 324, 330, 620 | ADC offset self-test: a measurement channel's zero offset is out of range at start-up |
| 88 | Arc fault reported by the AFCI module; the controller also forces the state to Fault (0x5500) |
| 100, 200, 202, 616, 715 | Hardware fault latch inputs |
| 107 | Off-grid output current above its limit |
| 113 | String power/current mismatch with the parallel relays closed |
| 133–136, 139 | Backup relay supervision: failed close cycles, inconsistent feedback, backup voltage differing from the grid by 30 V or more for 15 min |
| 201 | DC bus unbalance: one half-bus below a third of the bus voltage |
| 600, 601 | An input voltage above / below its limit for 8000 control cycles |
| 605 | Battery relay check failed during start-up |
| 997 | Frequency derating (over- or under-frequency curve) engaged; an alarm, not a fault |

Codes 5–7 and 15–17 exist in the numbering but this firmware never raises them. The conditions
behind codes 210–211 and 303–304 are not known.
Alarm bits that carry no code make the running state 0x9100 ("Running with alarm").

## Inverter fault codes (SG5.0RS)

The SG5.0RS power stage names each protection internally (the SH10RS firmware carries no names of
its own), and the numeric code the controller derives from each status bit follows the same
arithmetic as on the SH10RS. The table lists the codes with their meaning taken from those names. Codes marked "inferred" follow the arithmetic for
bits whose numbering the controller side has not confirmed. Classes: grid-side trips reconnect by
themselves after the grid returns; faults need a restart or a clear; alarms do not stop the
inverter.

| Code | Class | Meaning |
|---|---|---|
| 2 | grid | Grid over-voltage |
| 3 | grid | Grid transient (instantaneous) over-voltage |
| 4 | grid | Grid under-voltage |
| 8 | grid | Grid over-frequency |
| 9 | grid | Grid under-frequency |
| 10 | grid | Grid power outage (islanding detected) |
| 11 | fault | DC current injection into the grid too high |
| 12 | fault | Excess leakage current |
| 13 | grid | Grid abnormal (outside the connection window) |
| 14 | grid | 10-minute mean grid over-voltage |
| 19 | fault | DC bus instantaneous over-voltage |
| 20 | fault | DC bus average over-voltage |
| 21, 22 | PV | PV input 1 / 2 over-current |
| 24 | fault | DC bus capacitor unbalance (averaged) |
| 25 | fault | DC bus capacitor unbalance (instantaneous) |
| 28, 29 | PV | PV input 1 / 2 reverse polarity |
| 36 | fault | Inverter bridge (IGBT) over-temperature |
| 37 | fault | Internal ambient over-temperature |
| 38 | fault | Output relay fault |
| 39 | fault | Low insulation resistance (ISO) |
| 41 | fault | Leakage-current sensor fault |
| 43 | fault | Internal ambient under-temperature |
| 44 | fault | Inverter open-loop self-check failed |
| 48 | fault | Current-measurement channel fault |
| 71 | alarm | AC-side surge protection device alarm |
| 72 | alarm | DC-side surge protection device alarm |
| 88 | fault | Arc fault (AFCI) |
| 100 | fault | Hardware inverter over-current |
| 105 | fault | Grid self-check failed |
| 106 | fault | Ground wire fault |
| 200 | fault | Hardware DC bus over-voltage |
| 202 | PV | Hardware PV over-current |
| 204, 205, 209 | PV | Boost converter 1 / 2 / 3 short circuit |
| 207 | PV | PV input 3 over-current |
| 208, 212 | PV | PV input 3 / 4 reverse polarity |
| 306 | fault | Input and output power do not match |
| 309 | fault | Redundant voltage-measurement channel fault |
| 312 | fault | Redundant DC-current-measurement channel fault |
| 315 | PV | PV input 1 current-measurement channel fault |
| 316, 324, 328 (inferred) | PV | PV input 2 / 3 / 4 current-measurement channel fault |
| 541, 542 (inferred) | alarm | Ambient temperature sensor open / short |
| 543, 544 (inferred) | alarm | Inverter temperature sensor open / short |
| 545, 546 (inferred) | alarm | Boost temperature sensor open / short |
| 551 (inferred) | alarm | Fan blocked |
