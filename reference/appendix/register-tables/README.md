# Register tables

These pages list every Modbus register the dongle firmware knows about, one row per register
(or per multi-register value), as listed in the WiNet-S2 firmware's poll and Modbus TCP maps.
They are reference material for the narrative pages; for what the registers mean in practice,
start with [SH10RS registers](../../devices/sh10rs/registers.md),
[SG5.0RS registers](../../devices/sg5rs/registers.md) and
[SBR192 registers via the inverter](../../devices/sbr192/registers-via-inverter.md).

## Two kinds of table

- **RS-485 poll maps** (`rs485-*.md`) are what the dongle reads from (or writes to) each device on
  its RS-485 bus. The inverter map is the dongle's own private map of the inverter: the identity
  block at 2500 and up, the private 5200-series block and the 30000-series parameters, including
  blocks that Sungrow's public documentation does not describe. Batteries, EV chargers and
  Energy Bridges are reached through the inverter, which answers for them on their own slave
  addresses. See [the RS-485 master](../../protocols/rs485-master.md).
- **Modbus TCP maps** (`modbus-tcp-*.md`) are what the dongle serves to LAN clients on port 502,
  one map per device type, in Sungrow's public addressing. Registers in these maps are answered from
  the dongle's poll cache; a holding-register read that touches an unmapped address is forwarded
  live (see [Modbus TCP](../../protocols/modbus-tcp.md)).

The WiMeter and WiProxy tables are poll maps too, but the dongle reaches those devices over Modbus
TCP rather than RS-485.

Most values appear in both kinds of table, though not always at the same address. For meters the
two address spaces are unrelated, so treat the meter TCP map and the meter poll maps as separate.

## Index

| Table | Named / total |
|---|---|
| [Modbus TCP map: string inverter (device types 1 and 21)](modbus-tcp-string-inverter.md) | 278 / 498 |
| [Modbus TCP map: hybrid (storage) inverter (device type 35)](modbus-tcp-hybrid-inverter.md) | 70 / 279 |
| [Modbus TCP map: meter and Energy Bridge (device type 18)](modbus-tcp-meter.md) | 26 / 34 |
| [Modbus TCP map: battery (device type 44)](modbus-tcp-battery.md) | 11 / 34 |
| [RS-485 poll map: inverters (protocol types 1/4, 2, 3 and 5)](rs485-inverters.md) | 306 / 847 |
| [RS-485 poll map: battery stack (device type 44, read through the inverter)](rs485-battery.md) | 18 / 115 |
| [RS-485 poll map: EV charger (device type 46)](rs485-ev-charger.md) | 25 / 62 |
| [RS-485 poll map: Energy Bridge (device type 92)](rs485-energy-bridge.md) | 41 / 46 |
| [RS-485 poll map: meter type 6100 (DTSD1352)](rs485-meter-dtsd1352.md) | 32 / 40 |
| [RS-485 poll map: meter type 6200 (DTSU666)](rs485-meter-dtsu666.md) | 23 / 28 |
| [RS-485 poll map: meter type 6300 (S100)](rs485-meter-s100.md) | 8 / 8 |
| [Modbus TCP poll map: WiMeter (device type 34, product 1819)](wimeter.md) | 11 / 12 |
| [Modbus TCP poll map: WiProxy (device type 34, product 1820)](wiproxy.md) | 11 / 12 |

"Named" rows have a short name and an English label; the label is what the dongle's web interface shows. Unnamed
rows are still exact in address, width and type: the dongle polls them (and passes them on to
iSolarCloud or serves them over Modbus TCP) but never displays them locally, so their meaning is
not given by the dongle.

## Columns

| Column | Meaning |
|---|---|
| address | 1-based register address, as in Sungrow's published Modbus documentation. The address sent on the wire is address − 1. |
| fn | Modbus function code in hex: `03` read holding registers, `04` read input registers, `10` write multiple registers, `77` a private Sungrow read code. See [Modbus function codes](../../protocols/modbus-function-codes.md). |
| count | Number of 16-bit registers the value occupies. |
| type | `u16`/`s16` one register; `u32`/`s32` two registers, **low word first**; `u64` four registers; `str` a byte string of 2 × count bytes. `f32` (meter tables only): a two-register IEEE 754 float32. |
| scale | Multiplier applied to the raw value: displayed = raw × scale. Blank means the raw value is shown as is. A scale of 0.001 with unit kW means the register itself is in W. |
| unit | Unit of the displayed (scaled) value. |
| field | The dongle's short name for the value (not sent by the web interface, which reports a translation key and the label). |
| label | English name the web interface shows. |
| poll | Nominal poll period (poll maps only), see below. |
| notes | Bit width for bitfields, and capability gating, see below. |
| protocol | Inverter poll map only: which protocol types include the row, see below. |

### Poll period

| Value | Meaning |
|---|---|
| every pass | Read on every round of the fast poll cycle, typically every few seconds. |
| 2 s, 3 s, 30 s, 60 s | Read at most once per that period. |
| 1 h | Identity and rating blocks, re-read hourly. |
| N s (for example 1203 s) | Read at most once every N seconds. |
| blank | Not polled cyclically: `10` rows are written on demand, `77` rows are read once when needed. |

The dongle merges consecutive rows with the same function code into one request, spanning small gaps,
up to 120 registers per read. A missing value reads back as a sentinel: `0xFFFF` (u16), `0x7FFF`
(s16), `0xFFFFFFFF` (u32), `0x7FFFFFFF` (s32), or a string whose first byte is 0. The dongle treats
a sentinel as "no data" rather than as a value.

### Notes

- **`32 bits`** (or another width): the register is a bitfield. The dongle splits it into that many
  individual status flags, each reported as its own value.
- **`capability w.b`**: the row is polled only when bit *b* of word *w* of the inverter's capability
  block is set. The capability block is 60 registers at address 5520 (input registers); the inverter
  uses it to announce which optional feature groups (extra MPPTs, battery, backup output and so on)
  it has. Protocol types 2 and 3 read the block on every pass.

### Protocol type

The inverter poll map is the union of four tables, one per protocol type: `1/4` (one table shared
by types 1 and 4), `2`, `3` and `5`. The `protocol` column lists the tables that contain the row; a
row is polled from a given inverter only if it is in that inverter's table. The dongle picks the
protocol type at discovery from the inverter's identity registers (device code and protocol code in
the 2500 block). Both the SH10RS hybrid and the SG5.0RS string inverter are protocol type 3. See
[device discovery](../../protocols/device-discovery.md).
