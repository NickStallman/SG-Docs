# SG5.0RS registers: differences from the SH10RS

The SG5.0RS reports the same protocol code (`AA10`) and tag (`ZS`) as the SH10RS, so a dongle polls both with the same
private 5xxx map (protocol type 3, incremental function `0x65` with the table rows behind it). Most registers therefore
mean the same on both; they are described on [../sh10rs/registers.md](../sh10rs/registers.md), and the full row list
the dongle uses is the generated table
[../../appendix/register-tables/rs485-inverters.md](../../appendix/register-tables/rs485-inverters.md). This page lists
only what differs on the string inverter.

Register numbers are 1-based, as in Sungrow's Modbus documentation; the address in the frame is one less. Unless a
row says otherwise, registers are input registers (function 4), and 32-bit values are two registers, low word first.

## 1. What the SG5.0RS does not serve

| SH10RS group | Registers | On the SG5.0RS |
|---|---|---|
| Battery | 5214, 5629–5639, 5666–5672, 13041 | Not served |
| Backup (EPS) port | 5720–5734 | Not served |
| Power-flow flags | 6099 | Not served |
| Load and export power | 5216, 5218 | Not served |
| PV yield and AC input/output energy | 5660–5664, 5676–5680 | Not served (the SG5.0RS reports yields at 5223–5228 instead) |
| Package, module and battery version strings | 8801, 8816, 8831 | Not served |
| Hybrid public map | 13000–13370 | No such window |
| Battery tunnel | 19101–20000 | No such window |

Meter rows (5601–5607, 5611, 5615, 5737–5746) exist in the map but carry placeholders when no meter is attached to the
inverter's meter port: −0.01 A (raw 0xFFFF) in the meter current rows and 0 Hz in the meter frequency. Their values
mean nothing unless the meter active power at 5601 is valid. Phase B and C voltage and current (5248, 5249,
5251, 5252) read 0 on the single-phase SG5.0RS, where the SH10RS serves 0xFFFF.

## 2. Registers specific to the string inverter

| Register | Type | Unit, scale | Meaning | Value on a string-inverter site |
|---|---|---|---|---|
| 5223 | u16 | kWh, 0.1 | Daily yield (AC) | |
| 5224 | u32 | kWh, 0.1 | Monthly yield | |
| 5226 | u32 | kWh, 0.1 | Yearly yield | |
| 5228 | u32 | kWh, 0.1 | Total yield | |
| 5230 | u32 | h | Total on-grid operating time | |
| 5240 | u16 | min | Daily on-grid operating time; resets daily | |
| 5243 | s16 | V, 0.1 | Negative voltage to ground | 0 |
| 5503 | u16 | enum | DRM state, served directly: 1–9 = DRM0–DRM8, 255 = no DRM dispatching | 255 |
| 5507 | u16 | enum | Reactive-power regulation mode in force | 5, Q(U) (volt-var) |
| 5509 | u32 | W | Active-power limit setpoint in force; equals the rating when limiting is off | 5000 |
| 7853 | u16 | code | Meter type detected on the inverter's meter port, 0 when none ([meters](../meters.md)) | |

The SH10RS does not serve 5223–5240 or 5507, and its 5503 is hidden from a dongle that honours the capability bitmap
while no DRM mode is asserted. The SH10RS reports its detected meter type at 13136, not 7853.

Reactive-power mode values (5507): 0 off, 1 LVRT, 2 fixed Q(T), 3 fixed power factor, 4 Q(P), 5 Q(U), 6 HVRT, 7 SVG
mode.

## 3. Identity and ratings

| Register | Meaning | SG5.0RS |
|---|---|---|
| 2515 | Device code | 9734 (`0x2606`) |
| 2516–2523 | Model | `SG5.0RS` |
| 2534–2535 | Protocol code (ASCII) | `AA10` |
| 2578 | Protocol tag (ASCII) | `ZS` |
| 2582 | Installed controller build (first of eight version slots) | `SUNSTONE-S_01011.01.49` |
| 4954 | ARM software identifier | `ARM_SUNSTONE-S_V11_V01_A` |
| 4969 | DSP software identifier | `MDSP_SUNSTONE-S_V11_V01_A` |
| 5374 | Rated active power, 0.1 kW | 5 kW |
| 5375 | Output type | 0, single-phase |
| 5376 | Rated reactive power, 0.1 kvar | 3 kvar |
| 5659 | Maximum apparent power, 0.1 kVA | 5 kVA |
| 5333–5336 | MPPT 1 and 2 voltage and current | served; MPPT 3 and up are not |
| 5487–5488 | Function flags (u32) | build constant `0x025040DD` plus two runtime bits (13 and 15) |
| 5489–5490 | Function flags 2 (u32) | `0x00020000`; bit 4 (the 115200-baud capability) is clear, so the dongle stays at 9600 baud |
| 34209 (holding) | Model, second field | `SG5.0RS` (empty on the SH10RS) |

Capability bitmap (5520–5579) words of interest: word 29 is `0x1220` (bit 12 set) and word 32 is `0x0A20` (bit 5 set,
which tells the dongle that the grid-standard register 30036 exists). A function 4 read of 5548 returns exception 02.

## 4. Control and grid-code registers

| Register | Type | Meaning | Values |
|---|---|---|---|
| 5006 | holding u16 | Start / stop (power switch of the `unit` family) | `0xCF` start, `0xCE` stop |
| 32310 | holding u16 | Country | 6 = Australia |
| 32311 | holding u16 | Grid code | 0 on a string-inverter site |
| 30036 | holding u16 | Grid standard ("power company") | 12 = AS/NZS 4777.2:2015; 13 / 14 / 15 = AS/NZS 4777.2:2020 Australia A / B / C |
| 30037 | holding u16 | Grid type | not served (0xFFFF) on a string-inverter site |
| 5479 | input u16 | Grid-profile variant | 1 = PV variant |
| 30113 | holding u16 | DRM enable | 170 enable, 85 disable; exception 04 outside Australia / New Zealand |
| 30644 | holding u16 | Inverter type | 0xFFFF (not served); only protocol types 1 and 4 use it |
| 32565 | holding u16 | Transparent transmission via backup RS485 port | 170 enable, 85 prohibited ([architecture](architecture.md#5-address-gate-and-rs-485-relay)) |
| 32600 | holding u16 | Raw copy of USART6 traffic to the dongle port | 170 on, 85 off; not kept across a reboot |
| 30447 / 30448 | holding u16 | Communication anomaly detection / trigger delay | 170 / 85; 3–20 min |

**What the power stage produces.** The running state (5038) is built by the power stage from the
codes 0x0000 (running), 0x1200 (initial standby), 0x1300 (stopped by command; also reported during
a rapid shutdown), 0x1400 (standby), 0x1500 (emergency stop), 0x1600 (starting), 0x5500 (fault),
0x8100 (derating), 0x8200 (running at the dispatched power limit), 0x9100 (running with an alarm)
and 0xA100 (waiting for PV power). The active-power limit the
controller pushes down is kept by the power stage in 0.1 % steps in RAM only, with 110 % meaning
"no limit", so 5007 / 5008 revert after a power-stage restart. The power stage names every fault
and event internally and serves the names of the last four faults as text; the numeric codes are
listed in [error codes](../../appendix/error-codes.md#inverter-fault-codes-sg50rs).

**The PV variant.** The AS/NZS 4777.2:2020 options of the `unit` settings template come in two variants, chosen by
input register 5479: values 1 and 2 select the PV variant (39 register runs, 111 registers), values 3 to 6 the
energy-storage variant (41 runs, 121 registers). The SG5.0RS reads 1, so a grid-profile write sends the PV variant: the
country to 32310, the standard to 30036, then the variant's register runs in template order
([device types and grid profiles](../device-types-and-grid-profiles.md)).

## 5. Other readable values

| Register | Meaning |
|---|---|
| 2792–2799 | The masked session key of the encrypted Bluetooth link; readable in clear with function 4 on every port ([architecture](architecture.md#8-encrypted-serial-link-on-usart1-phone-app-over-bluetooth)) |
| 4815 | The inverter's own slave address; served on USART1 only |
| 4990–5000 | Serial number and device type, the one block (besides 2792) readable in clear on USART1 |
| 5506 | Power-limit reason, copied from the power stage. This model's power stage emits 0 (none), 1 (over-temperature or volt-watt derating), 2 (active-power limitation; also how a DRM limit applied through the power-limit ratio appears), 8 (over-frequency), 32 (soft start), 256 (volt-watt), 2048 (under-frequency) and 36864 (generation limit); it never emits 4 or 512 itself |
| 5519 | AFCI (arc-fault) trip count |
| 32538 (holding) | PAN id of the dormant power-line-modem configuration |

## Not yet known

- How the SG5.0RS validates holding-register writes (value ranges, cross-checks, gates by device state).
- The contents of most windows register by register: 9800–9850, 36000–36449 and the bulk of the private 5xxx map.
- The meaning of 5222 (device status), 5969 (AFCI self-test result) and 5970, which read 0, 2 and 170 on a string-inverter
  site.
