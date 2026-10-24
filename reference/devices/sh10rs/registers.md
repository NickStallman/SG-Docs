# SH10RS register reference

This page lists the registers a reader of the SH10RS actually needs, grouped by subject, with
type, scale and the model-specific details. It describes firmware package P069
(ARM `SUNSTONE-H_01011.02.69`). The complete lists, including every row the dongle polls, are
in [`../../appendix/register-tables/rs485-inverters.md`](../../appendix/register-tables/rs485-inverters.md)
(RS-485, private map) and
[`../../appendix/register-tables/modbus-tcp-hybrid-inverter.md`](../../appendix/register-tables/modbus-tcp-hybrid-inverter.md)
(the dongle's Modbus-TCP view).

> **Numbering.** All register numbers are 1-based, as in Sungrow's documents; the PDU address is
> one less. Unless a row says otherwise, registers are input registers (function code 4), 32-bit
> values are low word first, and "—" in the scale column means 1.

Columns used below: **Reg** = first register, **N** = register count, **Type** (u16, s16, u32, s32,
str = ASCII), **Scale** and **Unit** of one raw step. **Pub** gives the address of the same value in
Sungrow's published hybrid map, which the inverter also serves and which the dongle re-serves on
Modbus TCP.

## Identity and ratings

| Reg | N | Type | Scale, unit | Meaning | Pub | Notes |
|---|---|---|---|---|---|---|
| 2515 | 1 | u16 | — | device code | 5000 | 3355 = 0x0D1B for the SH10RS |
| 2516 | 8 | str | — | model | — | `SH10RS` |
| 2534 | 2 | str | — | protocol code | — | `AA10`. Read as text; as a u32 it is a meaningless 1094791472 |
| 2536 | 2 | u32 | — | protocol version | — | 0x01000800 (V1.0.8) |
| 2578 | 1 | str | — | protocol tag | — | `ZS` |
| 2582 | 120 | str | — | firmware builds, eight 15-register slots | — | slot 1 = ARM build `SUNSTONE-H_01011.02.69` |
| 4954 | 15 | str | — | ARM software identifier | 4954 | `ARM_SUNSTONE-H_V11_V01_A`; 5800 repeats it |
| 4969 | 15 | str | — | DSP software identifier | 4969 | `MDSP_SUNSTONE-H_V11_V01_A` |
| 5374 | 1 | u16 | 0.1 kW | rated active power | 5001 | 10.0 |
| 5375 | 1 | u16 | — | output type: 0 single-phase, 1 three-phase 3P4L, 2 three-phase 3P3L | — | 0 |
| 5376 | 1 | u16 | 0.1 kvar | rated reactive power | — | 6.0 |
| 5378 | 10 | str | — | serial number | 4990 | `A2xxxxxxxxx` |
| 5479 | 1 | u16 | — | machine type: selects the PV (1–2) or ES (3–6) variant of a grid profile | 5479 | 3 on the SH10RS |
| 5629 | 1 | u16 | — | battery type configured | — | 105 = Sungrow high-voltage Li-ion; 2 = no battery, 9 = Sungrow Li-ion, 109 = Sungrow LFP, 110 = Sungrow modular |
| 5659 | 1 | u16 | 0.1 kVA | maximum apparent power (rating) | — | 10.0 |
| 8801 | 15 | str | — | last firmware package installed | 13250 | `SUNSTONE-H_B000.V002.P0xx` |
| 8816 | 15 | str | — | communication module version, as last reported by the dongle | 13265 | e.g. `WINET-SV300.001.03.P0xx` |
| 8831 | 15 | str | — | battery control unit firmware | 13280 | `SBRBCU-S_22011.01.30` |
| 34209 | 15 | str | — | second model field (function code 3) | — | empty on the SH10RS |

## Running state and status

| Reg | N | Type | Meaning | Pub | Notes |
|---|---|---|---|---|---|
| 5221 | 1 | u16 enum | operating status (table below) | 13000 | 0x8200 "Dispatched running" is normal on a hybrid running under its own EMS, both while exporting and while charging |
| 6099 | 1 | u16 bits | power-flow flags (table below) | 13001 | e.g. 43 = PV + battery charging + load + importing |
| 5503 | 1 | u16 enum | DRM state | 13043 | gated by capability bit 5521.2, see [DRM](grid-codes-and-drm.md#demand-response-modes-drm) |
| 5504 | 1 | u16 | ripple-control state (also 13137) | — | always 255 on this build: the ripple mode never produces a state |
| 5506 | 1 | u16 enum | power-limitation reason (table below) | — | copied from the power-stage DSP |
| 5519 | 1 | u16 | AFCI (arc-fault) trip count | — | |
| 5485 | 1 | u16 | feature word | 5485 | 0x55 |

**Operating status** (5221; the dongle re-serves the same code at public 13000, and on string inverters at 5038):

| Code | Text | Code | Text |
|---|---|---|---|
| 0x0000 | Running | 0x1300 | Awaiting startup |
| 0x0400 | Running (maintain mode) | 0x1400 | Standby |
| 0x0800 | Running (forced mode) | 0x1500 | Emergency stop |
| 0x0801 | Running (backup mode) | 0x1600 | Starting |
| 0x1000 | Running (off-grid) | 0x1700 | AFCI self-test shutdown |
| 0x1202 | Running | 0x1800 | Intelligent station building |
| 0x2000 | Open loop | 0x1900 | Safe mode |
| 0x4000 | Running (external EMS) | 0x2500 | Communication fault |
| 0x4001 | Emergency charging | 0x2501 | Restarting |
| 0x0041 | Off-grid DC charging | 0x5500 | Fault |
| 0x1111 | Uninitialised | 0x6600 | Recovering from fault |
| 0x1200 | Initial standby | 0x8000 | Stop |
| 0x8100 | Derating running | 0x8200 | Dispatched running |
| 0x9100 | Running with alarm | | |

Sungrow's published hybrid document describes input 13000 differently, as one bit per state:
0x0001 stop, 0x0002 awaiting startup, 0x0004 emergency stop, 0x0008 standby, 0x0010 initial
standby, 0x0020 starting, 0x0040 running, 0x0080 derating, 0x0100 fault, 0x0200 update failed,
0x0400 maintain mode, 0x0800 forced mode, 0x1000 off-grid, 0x4000 external EMS. The dongle's
Modbus-TCP view of an SH10RS carries the 5221 code (0x8200) at 13000, not this bit coding.

**Power-flow flags** (6099 / public 13001):

| Bit | Meaning |
|---|---|
| 0 | PV generating |
| 1 | battery charging |
| 2 | battery discharging |
| 3 | load consuming |
| 4 | exporting to the grid |
| 5 | importing from the grid |
| 6 | reserved |
| 7 | load power negative (load feeding back) |

**Power-limitation reason** (5506). The inverter's ARM copies this word from the power-stage DSP.

| Code | Reason | Code | Reason |
|---|---|---|---|
| 0 | none | 256 | grid overvoltage |
| 1 | overtemperature | 257 | ripple control |
| 2 | active power limitation | 512 | DRM5–DRM8 |
| 4 | grid undervoltage | 1024 | DRM1–DRM4 |
| 5 | power factor below 1 | 2048 | grid underfrequency |
| 8 | grid overfrequency | 4096 | charging derating at low grid voltage |
| 11 | optimizer overtemperature | 8192 | reactive power regulation |
| 16 | feed-in limitation (without meter) | 16384 | high PV voltage |
| 32 | soft start | 32768 | high altitude |
| 64 | feed-in limitation | 32770 | undertemperature |
| 128 | PV input power limit | 32771 | alarm |
| 36864 | generation limit control | 32772 | module temperature abnormal |

The SH10RS substitutes **2049 (0x801)**, which is not in Sungrow's list, while a DSP flag is set
and the switch at holding 31214 (in the feed-in-limitation settings) is 0xAA. In the same
condition it raises alarm 997. It is probably a feed-in-limitation fail-safe. Whether
simultaneous reasons are combined bitwise is not confirmed.

## Faults and alarms

The active fault list at 5448–5472 (25 registers) is a structure of words, not a string:

| Reg | Content |
|---|---|
| 5448 | 0 |
| 5449 | flags: 0x100 = at least one fault, 0x80 = at least one alarm |
| 5450, 5451 | 0 |
| 5452 | number of entries (0–10) |
| 5453 + 2i | code of entry i (i = 0–9) |
| 5454 + 2i | class of entry i: 25 = fault, 24 = alarm, 0 = other |

The codes are Sungrow's numeric fault codes (for example 732 = a battery protection, 997 = the
alarm mentioned above). Their meanings are in
[`../../appendix/error-codes.md`](../../appendix/error-codes.md). The same layout is served on each
battery slave (200–204) for that stack's own faults, see
[battery-interface.md](battery-interface.md).

Battery-system protection and alarm bits from the battery master are folded into four 32-bit
flag words: 13070/13071, 13072/13073, 13074/13075 and 13078/13079 (13076/13077 is 0). Their bit
meanings are not published.

## Real-time AC, PV, grid and load values (private map)

| Reg | N | Type | Scale, unit | Meaning | Pub | Notes |
|---|---|---|---|---|---|---|
| 5214 | 2 | s32 | 1 W | battery power | 13022 (u16, unsigned) | + discharge, − charge |
| 5216 | 2 | s32 | 0.001 kW | export power (grid) | 13010 (W) | + export, − import |
| 5218 | 2 | s32 | 0.001 kW | load power | 13008 (W) | house load |
| 5232 | 2 | s32 | 0.001 kW | total active power (AC port) | 13034 (W) | |
| 5234 | 2 | s32 | 0.001 kvar | total reactive power | 5033 | |
| 5236 | 2 | u32 | 0.001 kW | total DC (PV) power | 5017 | |
| 5238 | 2 | u32 | 0.001 kVA | total apparent power | 5087 | |
| 5241 | 1 | s16 | 0.001 | power factor | 5035 | |
| 5242 | 1 | u16 | 0.01 Hz | grid frequency | 5242 | |
| 5244 | 1 | u16 | 0.1 V | internal DC bus voltage | — | ~455 V |
| 5245 | 1 | s16 | 0.1 °C | internal air temperature | 5008 | |
| 5246 | 1 | u16 | 1 kΩ | insulation resistance to ground | — | PV array insulation |
| 5247 | 1 | u16 | 0.1 V | AC voltage (phase A) | 5019 | |
| 5250 | 1 | s16 | 0.1 A | AC current (phase A) | 13031 | |
| 5248, 5249 | 1 each | u16 | 0.1 V | phase B/C voltage | 5020, 5021 | 0xFFFF on this single-phase model |
| 5251, 5252 | 1 each | s16 | 0.1 A | phase B/C current | 13032, 13033 | 0xFFFF, which decodes as −0.1 A: ignore |
| 5333–5340 | 8 | u16 | 0.1 V / 0.1 A | MPPT 1–4 voltage, current (V, I pairs) | 5011–5016, 5115/5116 | four trackers |
| 5509 | 2 | u32 | 1 W | active power limit in force | — | not served on the SH10RS |
| 5622, 5623 | 1 each | u16 | unit not confirmed (×10 W or 0.1 %) | export-limit setting range, min / max | 5622, 5623 | raw 0 / 1000; the inverter enforces this range on 13074 |

## Grid meter and backup port

| Reg | N | Type | Scale, unit | Meaning | Notes |
|---|---|---|---|---|---|
| 5601 | 2 | s32 | 1 W | meter active power | + import, − export |
| 5603, 5605, 5607 | 2 each | s32 | 1 W | meter phase A/B/C active power | |
| 5737 | 1 | u16 | 0.01 Hz | meter grid frequency | |
| 5738 | 2 | s32 | 1 var | meter reactive power | |
| 5741–5743 | 3 | u16 | 0.1 V | meter phase A/B/C voltage | a three-phase meter on a single-phase supply shows a few tenths of a volt on B/C |
| 5744–5746 | 3 | s16 | 0.01 A | meter phase A/B/C current | |
| 5720 | 1 | s16 | 0.1 A | backup port phase A current | B/C (5721/5722) not served |
| 5723 | 1 | s16 | 1 W | backup port phase A power | B/C at 5724/5725 |
| 5726 | 2 | s32 | 1 W | backup port total power | load on the EPS output |
| 5731 | 1 | u16 | 0.1 V | backup port phase A voltage | B/C (5732/5733) not served |
| 5734 | 1 | u16 | 0.01 Hz | backup port frequency | |

## Energy counters

All energies are 0.1 kWh. Daily counters are u16 and reset at midnight inverter time. Totals are u32.

| Reg | N | Meaning | Pub | Notes |
|---|---|---|---|---|
| 5660 / 5661 | 1 / 2 | daily / total PV yield | 13002 / 13003 | the hybrid's PV figure; 5223–5228 (string-inverter yields) are not served |
| 5676 / 5677 | 1 / 2 | daily / total AC output energy | 5003 / 5004 | energy out of the AC port |
| 5679 / 5680 | 1 / 2 | daily / total AC input energy | — | energy taken in through the AC port (grid charging) |
| 5663 / 5664 | 1 / 2 | daily / total PV export | 13005 / 13006 | PV energy fed to the grid |
| 5617 / 5619 | 2 / 2 | daily / total PV energy consumed directly by loads | 13017 / 13018 | |
| 5666 / 5667 | 1 / 2 | daily / total PV-to-battery charge | 13012 / 13013 | |
| 5611 | 2 | total export (meter) | 13046 | |
| 5615 | 2 | total import (meter) | 13037 | |
| 5609 / 5613 | 2 / 2 | daily export / daily import | 13045 / 13036 | **not served** at 5609/5613 on P069; read the published-map addresses |
| 5669 / 5670 | 1 / 2 | daily / total battery discharge | 13026 / 13027 | |
| 5672 | 1 | daily battery charge | 13040 | |
| 5673 / 5674 | 2 | total battery charge | 13041 / 13042 | **broken at 5673**: see below |
| 5675 | 1 | daily self-consumption rate, 0.1 % | 13029 | not served at 5675 on P069 |

**Total battery charge.** On P069, register 5673 carries only the **low 16 bits** of the
total-charge counter. Register 5674 is never written: it is skipped in the reply. Once the
total passes 6553.5 kWh the value at 5673/5674 wraps and is wrong. For example, a true
15751.7 kWh (0x2674D) reads as 2644.5 kWh (0x674D). The full 32-bit value, low word first, is
served at **13041/13042**. Use that pair.

## Battery values on the inverter's address

These come from the battery master's CAN frames, or from the inverter's own DC measurement, and
are served on the inverter's slave address. The per-stack values are on slaves 200–204, see
[battery-interface.md](battery-interface.md).

| Reg | N | Type | Scale, unit | Meaning | Pub | Notes |
|---|---|---|---|---|---|---|
| 5214 | 2 | s32 | 1 W | battery power | 13022 | a 16-bit value sign-extended; + discharge. 13022 is unsigned: take the direction from the power-flow bits |
| 5630 | 1 | u16 | 0.1 V | battery voltage (inverter measurement) | 13020 | 0 below 6.1 V |
| 5631 | 1 | s16 | 0.1 A | battery current | 13021 | + discharge |
| 5632 | 1 | s16 | 0.1 °C | battery temperature | 13025 | |
| 5633 | 1 | u16 | 0.1 % | battery SOC (system) | 13023 | |
| 5634 | 1 | u16 | 0.1 % | battery SOH (system minimum) | 13024 | |
| 5635 | 1 | u16 | 1 A | BMS maximum charge current | — | **forced to 0 at SOC 100.0 %**; copies at 13101/13102 on the dongle's Modbus-TCP view |
| 5636 | 1 | u16 | 1 A | BMS maximum discharge current | — | **forced to 0 at SOC 0**; copies at 13101/13102 on the dongle's Modbus-TCP view |
| 5637 | 2 | u32 | 0.1 kWh | BMS total discharge | — | not served on P069; use 5670; the dongle's own table labels it Wh |
| 5639 | 1 | u16 | 0.01 kWh | battery capacity | — | sum of the stacks' nominal energy (38.40 for two SBR192), or holding 33049 when that is non-zero; 0xFFFF when no stack is registered |
| 13039 | 1 | u16 | 0.1 kWh | battery system nominal energy, as reported by the battery master | 13039 | 384 = 38.4 kWh |
| 5669 / 5670 | 1 / 2 | u16 / u32 | 0.1 kWh | daily / total battery discharge | 13026 / 13027 | the total is the battery master's own counter (the sum of the stacks) |
| 5672 | 1 | u16 | 0.1 kWh | daily battery charge | 13040 | |
| 5673 / 5674 | 2 | u32 | 0.1 kWh | total battery charge | — | low 16 bits only, see above |
| 13041 / 13042 | 2 | u32 | 0.1 kWh | total battery charge | 13041 | full value; the one to use |
| 5666 / 5667 | 1 / 2 | u16 / u32 | 0.1 kWh | PV-to-battery charge | 13012 / 13013 | |

## Capability block 5520–5579

Input 5520–5579 is a bitmap of 60 words that tells a client which values this model serves.
Counting words from 1 at 5520, word *n* is register 5519 + *n*. The dongle reads the block before
anything else. For protocol types 2 and 3 it then skips every row whose capability bit is clear,
and clears any value it held for such a row.

| Bit | Register | Gates | SH10RS |
|---|---|---|---|
| word 2, bit 2 | 5521 bit 2 | DRM state 5503 | set **only while a DRM mode (state 1–9) is asserted**. So with DRM idle or disabled the dongle never shows a DRM state |
| word 2, bit 6 | 5521 bit 6 | power-limitation reason 5506 | set |

5521 reads 0xFF42 (DRM not asserted) and 0xFF46 while a DRM mode is active. A further bit,
word 32 bit 5 in the dongle's feature numbering, tells the dongle to write the grid-standard
register 30036 when it applies a grid profile. It is set on both RS models.

**Function flags** (input 5487 and 5489, u32 each) are mostly build-time constants:

| Reg | SH10RS value | Known bits |
|---|---|---|
| 5487 | 0x115940DD, plus bit 15 (a debounced external-link flag) and bit 21 (set for device codes 0x0D1A and 0x0D1B); live 0x1179C0DD | bit 22: the dongle adds a register block at 7816 to its poll. Bit 25: tells the dongle which model register to use |
| 5489 | 0x08020040 | bit 4: the inverter supports 115200 baud on the dongle link (clear) |

## What the dongle re-serves on Modbus TCP

For a device-type-35 inverter, the dongle's Modbus-TCP server answers input-register reads at
unit 1 in Sungrow's published hybrid layout. It builds those answers from the private-map values
it has polled over RS-485. It does not forward input reads to the inverter. Holding-register
reads and writes are forwarded to the inverter live. Details are in
[`../../protocols/modbus-tcp.md`](../../protocols/modbus-tcp.md).

| Published register(s) | Content on the dongle | Notes |
|---|---|---|
| 4990, 5000, 5001 | serial, device code, rated power | |
| 5003 / 5004 | daily / total AC output energy | from 5676 / 5677 |
| 5008, 5011–5017, 5019, 5033, 5035, 5087, 5115/5116, 5242 | temperature, MPPT 1–4, DC power, AC voltage, reactive power, PF, apparent power, frequency | |
| 5377, 5475, 5479 | country code (6), grid standard (13), machine type (3) | |
| 5601–5607, 5622/5623, 5631–5639, 5720–5738 | meter, export-limit range, battery, backup port | |
| 13000 / 13001 | operating status (5221 code) / power-flow flags | |
| 13002–13046 | yields, energies, battery values | 13022 battery power is unsigned |
| 13039 | nominal battery energy | |
| 13041 | total battery charge | the full value, not the truncated 5673 |
| 13043 (and 5503) | DRM state | reads 0 while the capability gate keeps the row unpolled; 0 is not a DRM code |
| 13101 / 13102 | BMS max charge / discharge current | copies of 5635 / 5636 |
| 13250, 13265, 13280 | package, communication module, battery firmware strings | |
| 2514–2593 | identity area | the dongle synthesises it: model at 2516, but no device code or serial; do not use it for identification |

Inside a mapped block, values the dongle does not have read as 0. A block outside the map
answers exception 2. On this model, input 13050–13068 and 13070–13079 read 0.

**Change stream.** Besides cyclic reads, Sungrow's private function code 0x65 carries a stream
of changed values. Its framing is described in
[`../../protocols/modbus-function-codes.md`](../../protocols/modbus-function-codes.md).

## Not served on P069

These rows exist in the private map but read 0xFFFF (or −1 when signed) on this firmware:

- 5223–5230 and 5240 (the string-inverter yields and running times);
- 5243 negative voltage to ground;
- 5253–5332 string voltages/currents and 5341–5372 MPPT 5–20;
- 5507 reactive-power mode and 5509 active power limit;
- 5609, 5613, 5637, 5675;
- 5721/5722 and 5732/5733 (backup phases B/C) and 8213–8215 (line-to-ground voltages);
- holding 33149/33150 (battery charging/discharging start power).

## Not yet known

- Which coding the inverter itself serves at input 13000 when read directly over RS-485.
- Whether 13036/13045 (daily import/export in the published map) carry data on P069.
- The meaning of the BMS flag words 13070–13079 and of the 5487/5489 bits other than 4, 15, 21, 22 and 25.
- Which of these registers the 0x65 change stream covers.
