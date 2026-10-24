# SH10RS hybrid inverter (SH-RS family)

The SH10RS is Sungrow's 10 kW **single-phase hybrid** inverter. One box holds a PV inverter with
four MPPT inputs, a bidirectional DC/DC stage for a high-voltage battery, a grid port and a
**backup (EPS) port** that keeps selected loads running when the grid fails. It reads the grid
meter wired to its own meter port. It also talks to the battery's control unit over CAN and
serves the battery's data to the dongle on its own RS-485 port. The smaller ratings of the SH-RS
range probably run the same `SUNSTONE-H` firmware family. The pages in this directory
describe the SH10RS with firmware package **SUNSTONE-H_B000.V002.P069** (ARM build
`SUNSTONE-H_01011.02.69`). The example values come from a hybrid site (SH10RS with two SBR192
stacks) behind a WiNet-S2.

For the battery itself see [`../sbr192/`](../sbr192/). For the string-inverter sibling that shares
most of this register model see [`../sg5rs/`](../sg5rs/).

## At a glance

| Item | SH10RS |
|---|---|
| Type | single-phase hybrid, grid-tied with backup port |
| Rated active power (input 5374) | 10.0 kW |
| Rated reactive power (input 5376) | 6.0 kvar |
| Maximum apparent power (input 5659) | 10.0 kVA |
| Output type (input 5375) | 0 = single-phase |
| PV inputs | 4 MPPT |
| Battery | high-voltage Li-ion (SBR stacks on CAN at 250 kbit/s; the inverter has five stack slots, the battery firmware addresses at most four); battery type 105 at input 5629 |
| Grid meter | inverter-attached; its readings appear as inverter registers (5601–5607, 5737–5746), not as a separate meter device |
| Main controller | STM32F417ZG (ARM firmware `SUNSTONE-H_01011.xx.yy`) |
| Other processors | master power-stage DSP (`SUNSTONE-H_03021.xx.yy`), slave DSP (`SUNSTONE-H_04011.xx.yy`), arc-fault detector (`AFCI_06002.xx.yy`) |
| Dongle-facing link | RS-485, 9600 baud 8N1, Modbus RTU, inverter at slave address 1 |

## Identity

How a dongle, or any Modbus client, recognises the SH10RS. All registers are input registers (function code 4).

| Field | Register(s) | SH10RS value | Notes |
|---|---|---|---|
| Device code | 2515 (Sungrow's public map: 5000) | 3355 = 0x0D1B | numeric model code; identifies the exact rating |
| Model string | 2516–2523 (8 registers, ASCII) | `SH10RS` | the family pattern is `SHx.xRS` / `SH10RS` |
| Protocol code | 2534–2535 | ASCII `AA10` (served as `10`, `AA`) | the same code appears on the SG5.0RS and on the battery slaves |
| Protocol version | 2536–2537 | `V1.0.8` | |
| Protocol tag | 2578 | ASCII `ZS` | `ZS` with `AA10` makes the dongle classify the inverter as **protocol type 3** |
| Firmware build | 2582 (120 registers, eight 15-register slots) | `SUNSTONE-H_01011.02.69` | the installed ARM build |
| ARM software identifier | 4954 (15 registers) | `ARM_SUNSTONE-H_V11_V01_A` | stays the same across builds; repeated at 5800 |
| DSP software identifier | 4969 (15 registers) | `MDSP_SUNSTONE-H_V11_V01_A` | |
| Serial number | 5378 (10 registers; public map 4990) | `A2xxxxxxxxx` | |
| Last installed package | 8801 (15 registers; public map 13250) | `SUNSTONE-H_B000.V002.P0xx` | |
| Communication module version | 8816 (public map 13265) | `WINET-SV300.001.03.P0xx` | whatever the dongle last reported to the inverter |
| Battery firmware | 8831 (public map 13280) | `SBRBCU-S_22011.01.30` | the battery control unit build |
| Inverter type | holding 30644 | 0xFFFF (not served), read as type 0 | irrelevant here: protocol type 3 always uses the `unit` settings family ([grid codes](grid-codes-and-drm.md)) |
| Machine type / variant selector | 5479 | 3 | 1–2 select the PV variant of a grid profile, 3–6 the ES (storage) variant |

**Classification by the dongle.** With device code 0x0D1B, protocol code `AA10` and tag `ZS`, the
dongle files the inverter as **device type 35, hybrid inverter**, protocol type 3. It then polls
it with Sungrow's private 5000-series map and reports it to iSolarCloud as device type 14. The
SG5.0RS goes through the same steps with device code 0x2606 and ends up as device type 21, string
inverter, also protocol type 3.

## Pages in this directory

| Page | What it covers |
|---|---|
| [architecture.md](architecture.md) | the processors inside the inverter, how the ARM, the DSPs, the AFCI board and the battery CAN fit together |
| [register-windows.md](register-windows.md) | which address ranges the inverter serves, which ones it forwards to the DSP or the battery, and which ones it refuses |
| [registers.md](registers.md) | curated reference of the readable values: identity, live power, energy counters, battery values, status, faults, DRM, capability bits |
| [control-and-ems.md](control-and-ems.md) | writable control: start/stop, EMS mode, forced charge/discharge, SOC window, export limit, backup reserve, battery power caps |
| [grid-codes-and-drm.md](grid-codes-and-drm.md) | country and grid-code model, what a grid-profile write changes, and the AS/NZS 4777 demand-response (DRM) input |
| [battery-interface.md](battery-interface.md) | the CAN protocol to the SBR battery and the battery registers the inverter serves on slaves 200–204 |
| [firmware-update.md](firmware-update.md) | how the inverter, DSP, AFCI and battery images are delivered and flashed |

## Reading registers: a primer

**Addressing.** Every register number in these pages is the **1-based** number used in Sungrow's
published Modbus documents. The address in the Modbus PDU is one less. For example, a read of the
battery SOC at 13023 sends start address 13022 (0x32DE) on the wire.

**Input vs holding.**

| Kind | Read | Write | What lives there |
|---|---|---|---|
| Input registers | function code 4 | — | measurements, counters, status, identity (2500s, 4950–5999, 6100–8900, 13000–13370) |
| Holding registers | function code 3 | function code 6 (one register) or 16 (several) | clock (5000–5005), start/stop (5006), EMS and battery controls (13000–13100, 33000–33300), grid and protection settings (30000–34999) |

The same number can exist in both spaces with different meanings. For example, input 13000 is
the running state, while holding 13000 is a start/stop mirror.

**Two maps on one inverter.** The SH10RS answers on two overlapping layouts:

- the **private map** (input 5000–5999, mostly 5200–5750), which the dongle polls for protocol-type-3 inverters;
- Sungrow's **published hybrid map** (input 4990–5130 and 13000–13370).

Many values appear in both, for example battery voltage at 5630 and at 13020. Where they differ,
[registers.md](registers.md) says which one to use.

**Multi-register values.**

- 32-bit values (`u32`, `s32`) occupy two registers with the **low word first**. Raw words
  0xB2AD, 0x0004 at 5004/5005 mean 0x0004B2AD = 307885, which is 30788.5 kWh at 0.1 kWh.
- Start every read of a 32-bit value on its first register. Some battery pairs return nothing
  for a read that starts on the second word.
- Strings are ASCII, two characters per register, high byte first, padded with NUL.

**Signed values and placeholders.**

| Type | "No data" value |
|---|---|
| u16 | 0xFFFF |
| s16 | 0x7FFF |
| u32 | 0xFFFFFFFF |
| s32 | 0x7FFFFFFF |

Values the inverter does not implement read back as 0xFFFF. In a signed 16-bit register that
decodes as −1 raw, which is why phase B/C current on this single-phase model reads −0.1 A
instead of "no data".

**Scale conventions.**

| Quantity | Typical scale |
|---|---|
| Voltage | 0.1 V |
| AC current | 0.1 A (meter phase currents 0.01 A) |
| Frequency | 0.01 Hz |
| Temperature | 0.1 °C, signed |
| Power, private map | s32 in 0.001 kW (5216–5238), or s32/s16 in W (5214, 5601–5607, 5720–5726) |
| Power, published map | W or 0.001 kW, see the tables |
| Energy | 0.1 kWh; daily counters u16, totals u32 |
| SOC / SOH | 0.1 % on inverter registers (5633/5634, 13023/13024) |
| Power factor | 0.001, signed |
| Ratings | 0.1 kW / 0.1 kvar / 0.1 kVA |

**Sign conventions.**

| Value | + means | − means |
|---|---|---|
| Export power 5216 | exporting to the grid | importing from the grid |
| Meter active power 5601 | importing | exporting |
| Battery power 5214, battery current 5631 | discharging | charging |

**Capability gating.** Input 5520–5579 is a 60-word capability bitmap. The dongle reads it first
and skips any value whose capability bit is clear. Details are in
[registers.md](registers.md#capability-block-55205579).

**Pacing.** The inverter shares one RS-485 bus with whatever else is attached. Leave at least
18 ms between a reply and the next request. The complete register lists are in
[`../../appendix/register-tables/rs485-inverters.md`](../../appendix/register-tables/rs485-inverters.md)
and [`../../appendix/register-tables/modbus-tcp-hybrid-inverter.md`](../../appendix/register-tables/modbus-tcp-hybrid-inverter.md).
