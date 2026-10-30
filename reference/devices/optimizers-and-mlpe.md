# Optimizers and MLPE (SP600)

No optimizer network was connected (both inverters report no optimizers); this page describes how the dongle,
inverter and MLPE firmware treat one.

Sungrow's module-level power electronics (MLPE) are optimizers and rapid-shutdown (RSD) devices on the PV strings. In
the dongle firmware the data model for them is called **SP600**. They are hosted by the inverter: the dongle never
addresses an optimizer directly. Every optimizer frame goes over the dongle's RS-485 port to the **inverter's own
slave address**, with Sungrow-private function codes, and the inverter relays it unchanged to the optimizer controller
behind it. Only the **WiNet-S2** speaks this protocol; the WiNet-S drops every reply with a function code from `0x70`
to `0x7A`.

Register numbers are 1-based, as in Sungrow's Modbus documentation; the address in the frame is one less.

```mermaid
flowchart LR
    CLOUD["iSolarCloud"] -- "commands 10383, 10451, 10452, 10453" --> S2["WiNet-S2"]
    S2 -- "RS-485, fn 0x70-0x7A to the inverter's address" --> ARM["Inverter controller"]
    ARM -- "USART6, frame relayed unchanged" --> CSU["MLPE-CSU"]
    CSU -- "SPI, register mailboxes" --> MODEM["MPLC power-line modem chips (up to 3)"]
    MODEM -- "power line on the DC string" --> OPT["Optimizers and RSD devices (SPLC firmware)"]
```

## 1. The private function codes

Frames are Modbus-RTU shaped (address, function, payload, CRC-16 low byte first) with vendor function codes:

| Fn | Use | Reply length the dongle expects |
|---|---|---|
| 0x74 | String list: number of strings, and per string its id and optimizer count | `BE16(bytes 4–5)` + 8 |
| 0x70 | Per-string run data: string header, then one block per optimizer (id, 32-bit fault word, online flag, data) | same |
| 0x73 | Per-string serial-number list: 21-byte records (1 id byte, 20-byte serial number) | same |
| 0x75 | Topology apply: the string / optimizer serial-number tree, 11 optimizers per frame | 6 bytes |
| 0x76 | Clone of "write multiple registers" on the inverter's optimizer register window | 8 bytes |
| 0x77 | Clone of "read holding registers" on that window (14503–14725) | `byte 2` + 5 |
| 0x71 | Optimizer parameter write | 8 bytes |
| 0x72 | Optimizer parameter read, and the read-back after a write | `BE16(bytes 6–7)` + 10 |
| 0x79 | Optimizer replace: up to 5 records of 48 bytes per frame | 8 bytes |
| 0x7A | Length rule only; no sender exists | `BE16(bytes 6–7)` + 10 |

Exception replies are `fn | 0x80`; the dongle treats `0xF1` and `0xF2` as the exceptions of `0x71` and `0x72`.

### 1.1 Block reads 0x70, 0x73, 0x74

Request (10 bytes): address, function, **selector** (0 for `0x74`; the string id for `0x70` and `0x73`), 0, total
packets (0 on the first request, then copied from the reply), packet number (1, then last + 1), two zero bytes, CRC.

Reply: address, function, total packets, packet number, data length N (u16 big-endian), N data bytes, CRC. The dongle
appends packets into a 1500-byte buffer and processes the result when packet number equals total.

| Reply | Data |
|---|---|
| 0x74 | strings N, then N pairs (string id, optimizer count); at most 49 bytes, so at most 24 strings |
| 0x73 | string id, record count (must equal that string's optimizer count), then the 21-byte records; serial characters must be `[0-9A-Za-z]` |
| 0x70 | string id, header length h (at most 12) and header, block count n, block size s (at most 27), then n blocks; at most 50 blocks, online flag below 2, at most 5 fault bits set |

### 1.2 Topology apply 0x75

Built from a topology file (JSON: the inverter's `sn`, `childs` = strings, each with `childs` = optimizers carrying
`devCode` and `sn`):

| Offset | Field |
|---|---|
| 0 | Inverter slave address |
| 1 | `0x75` |
| 2 | Number of strings in the file |
| 3 | Current string, 1-based |
| 4 | Frames in this string, `ceil(optimizers / 11)` |
| 5 | Current frame, 1-based |
| 6–7 | Payload length, u16 big-endian: `2 + 21 × k` |
| 8 | String code (numeric `devCode` of the string) |
| 9 | k, optimizers in this frame (1–11) |
| 10… | k records: numeric `devCode`, 20-byte serial number |

An empty topology is one 10-byte frame `[addr] 75 00 00 01 01 00 00` + CRC. The 6-byte reply's two data bytes are not
checked. A missing reply re-sends the frame up to 5 times.

### 1.3 Modbus clones 0x76 and 0x77

Standard Modbus layouts with a different function code. The registers the WiNet-S2 uses:

| Register | Fn | Use |
|---|---|---|
| 14503–14725 (18 table rows, including 14573, 14574, 14581, 14582, 14586–14591, 14701 × 25) | 0x77 | Cyclic polling once optimizers are present |
| 14573 × 22 | 0x77 | Fixed read while the optimizer list is not empty |
| 14574 × 3 | 0x77 | Disconnect-detection status (low byte status, high byte progress) |
| 14586 × 5 | 0x77 | Auto-search status, reason and progress |
| 14550 | 0x76 | Written with `0xA000` when an auto-search fails; the same register selects the MLPE log files in a log export |
| 14510, 14518 | 0x76 | The only writes the dongle passes while optimizers are present: one register, value 85 (stop) |

## 2. The session on the WiNet-S2

| Trigger | Action |
|---|---|
| Input 6002 ("optimizer present", capability-gated) goes 0 → 1 | Add the `0x77` polling rows and queue the first `0x74` |
| `0x74` reply | Per string, create a `0x70` request (every poll pass) and a `0x73` request (hourly) |
| Sum of optimizer counts differs from 14588 (optimizer count) | Reset the session and query `0x74` again |
| 14581 (attribute anchor) changes | Reset the session, new `0x74` |
| 14582 (one-key search) = 3, done | Reset the session, new `0x74` |
| 14586 (networking state) = 1 | Stop `0x70` / `0x73` while the network forms |
| 6002 goes 1 → 0 | Drop the session and its data |

Retransmission: `0x70`, `0x73` and the `0x77` read of 14701 are re-sent up to 10 transmissions in total on a timeout,
UART error, malformed or mismatched reply. Every other code is sent once. A failed first packet of `0x74` re-arms the
query at the next round, without limit. An exception reply to `0x70`, `0x73` or `0x74` resets the session.

## 3. Inverter relay

On both inverters the controller passes any frame with a valid CRC and a function code in `0x70`–`0x7E` or
`0xF0`–`0xFE` that arrives on the **dongle port** to its internal USART6 (115200 baud), whatever its slave address,
through a four-slot queue served every 50 ms; replies come back the same way. The relay is suspended during a
firmware-update session, and the inverter never reports a relay failure. The SG5.0RS details (including a raw USART6
copy switched by holding register 32600) are in [sg5rs/architecture.md](sg5rs/architecture.md#9-optimizer-mlpe-link-on-usart6).
The inverter also polls the module itself with function `0x17` (read start 14200–14349) and relays MLPE firmware
images to it.

## 4. The MLPE-CSU

On the SG5.0RS the module behind USART6 is the **MLPE-CSU** (image `MLPE-CSU_38011.01.69`, an STM32F417). It, not a
separate "SP600" box, answers the private codes.

| Item | Behaviour |
|---|---|
| Inverter-facing port | USART1, 115200, an always-on Modbus slave; functions 3, 4, 6, 0x10, and `0x17`, `0x70`–`0x7F` when addressed to the CSU's own address (or for `0x17`) |
| Private handlers | `0x17`, `0x70`–`0x77`, `0x79`, `0x7A`. Unless an internal enable is set, only `0x17`, `0x75`, `0x76` and `0x77` are served and the others get exception 04; `0x71` and `0x72` also need a second flag (else exception 06). `0x78` and `0x7B`–`0x7F` get no answer |
| UART4 | Monitor output: a copy of every power-line frame and of each modem firmware block; optionally a second slave |
| USART6 | 38400 baud bridge to an unidentified downstream device, active only in a transparent mode |
| Optimizer poll | A task cycles over the known devices; 600 consecutive failures raise a power-line-fault event to the inverter |
| Storage | I2C EEPROM and SPI NOR flash; firmware images are staged in the flash |
| Log files | `FaultRecord.txt`, `EventRecord.txt`, `UpdateRecord.txt`, `IV_MLPE.txt` (I-V curves), `OPTFaultRecord.txt`, `OPTEventRecord.txt`, `MainOPTPlcCommun.txt`, `OPTopology.txt` (topology), `PLCLQIInfo.txt` (link quality); selected through register 14550 in a log export |
| Events to the inverter | module update finished, power-line restart or load step, power-line link fault |

## 5. The MPLC power-line modems

The CSU drives up to three power-line modem chips, identified as **HT8584** in the CSU firmware, on its SPI bus (mode 3, one chip
select each). The **MPLC** firmware (`MLPE-MPLC_39011.30.23`) is not code for a separate microcontroller; it is 8051
firmware that runs inside these chips. Its identity block carries the vendor code `JQ`, which suggests a Hi-Trend part.

| Item | Behaviour |
|---|---|
| Host interface | Register-style SPI: a command byte, 32-bit data least significant byte first; a command register, an address register, a data register and a burst-read window |
| Mailboxes | One transmit and two receive mailboxes per chip, with a "frame waiting" flag |
| Power-line frame (as the CSU builds it) | command, sequence (u16), flag, length, payload, CRC-16; replies are the request command + `0x10` |
| MPLC update (module code 39) | The CSU writes the image into each chip's code memory in 128-byte blocks, reads each back and compares (up to 10 retries) |
| SPLC update (module code 40) | The slave firmware inside each optimizer: staged in the modem, which distributes it over the power line; the CSU then checks every device's reported version |
| KUROYOROCK (code 32) and RSD (code 45) updates | Sent by the CSU itself in power-line relay frames, each step closed with a CRC-8 |

Modulation, carrier band, coupling and the over-the-wire frame format live inside the chips and are not known.

## 6. Dormant PLC-modem driver in the SG5.0RS controller

The SG5.0RS controller also contains a driver for a modem wired directly to USART6 (two frame dialects starting `16 02`
and `16 16`, and a `Slave:` text-command node for PAN id, short address, tone mask and gain). In package P051 it never
transmits. It does not match the shipped chain, in which the modems sit behind the
CSU; it probably belongs to another board variant. The SH10RS has none of it.

## 7. Cloud and local commands

| Command | What it starts on the WiNet-S2 |
|---|---|
| 10383 | Optimizer search |
| 10451 | Optimizer parameter write (function `0x71`, followed by a `0x72` read-back) |
| 10452 | Topology file download to the dongle, then the `0x75` apply sequence |
| 10453 | Optimizer parameter read (function `0x72`) |
| Web service `optautosearch` | Auto-search and disconnect detection; both lock the port for other jobs while they run |

Cloud and app pass-through commands can also carry `0x70`–`0x74` frames; the dongle rebuilds them before sending, and
slices a `0x71` payload into pieces of at most 249 bytes.

## 8. Rapid shutdown

RSD devices are members of the same power-line network as the optimizers: the CSU keeps them in its device table,
reports their firmware as `RSD_45011`, and updates them with the relay mechanism of section 5. While optimizers are
present the dongle allows only one kind of write into the optimizer register window: 85 (stop) to 14510 or 14518.

## 9. Quirks of the WiNet-S2 session

- Only the last record's serial number is effectively validated in a `0x73` reply.
- The reassembly length keeps growing past the 1500-byte buffer.
- A silent optimizer network re-queues `0x74` at every round with no limit.
- The `0x75` reply bytes are not checked.
- A bad `0x73` reply does not reset the session (the test looks at the selector byte, not the function code).
- The string-list change test compares string ids only, not optimizer counts.

## Not yet known

- The meaning of the `0x70` string header and per-optimizer data bytes, the byte order of the fault word, and the
  record contents of `0x71`, `0x72` and `0x79`.
- The purpose of the `0xA000` written to 14550 after a failed search.
- The payloads of most power-line commands and replies, and what the CSU's own USART6 connects to.
- The CSU's own slave address on its inverter link.
- The modulation and band on the DC string, and the exact identity of the modem chip.
