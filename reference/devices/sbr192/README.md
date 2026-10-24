# SBR battery (SBR192)

The Sungrow SBR is a modular high-voltage LFP battery for the SH-RS hybrid inverters. A stack is
a column of 64 V modules with a **battery control unit** (BCU) on top. The BCU measures every
cell through a daisy chain of cell monitors, runs all battery management (SOC, SOH, balancing,
protections, current limits, contactors), and reports to the inverter over CAN. The inverter
republishes the data as Modbus slaves 200–204; the dongle reaches the battery only that way.

These pages describe an **SBR192** (six modules, 19.2 kWh) with BCU firmware
`SBRBCU-S_22011.01.30`, as found on a hybrid site with an SH10RS and two SBR192 stacks. The
other models of the family differ only in module count and use the same control unit and
firmware.

## The family

| Model | Modules | Cells in series | Nominal voltage | Energy | Model code (10710) | Rated power (10696) |
|---|---|---|---|---|---|---|
| SBR096 | 3 | 60 | 192 V | 9.6 kWh | 0x20E7 | 5760 W |
| SBR128 | 4 | 80 | 256 V | 12.8 kWh | 0x20E8 | 7680 W |
| SBR160 | 5 | 100 | 320 V | 16.0 kWh | 0x20E9 | 9600 W |
| **SBR192** | **6** | **120** | **384 V** | **19.2 kWh** | **0x20EA** | **11520 W** |
| SBR224 | 7 | 140 | 448 V | 22.4 kWh | 0x20EB | 13440 W |
| SBR256 | 8 | 160 | 512 V | 25.6 kWh | 0x20EC | 15360 W |

The model code is 0x20E6 + (modules − 2) and the rated power is modules × 64 V × 30 A. The BCU
finds the module count itself at start-up by counting the monitor chips on its chain.

## Pages

| Page | Content |
|---|---|
| [architecture.md](architecture.md) | the control unit, the modules, task structure, operating states, contactors, and what the inverter does and does not do |
| [can-bus.md](can-bus.md) | every CAN identifier and frame layout on CAN1, the CAN2 service bus, the tool tunnel, the status port and the record export |
| [cell-monitoring.md](cell-monitoring.md) | the isoSPI cell-monitor chain, the measurement cycle, fault levels, the deep-discharge latch and the module EEPROM |
| [balancing-and-soc.md](balancing-and-soc.md) | when and how cells are balanced, and how SOC, SOH, cycles and energy are counted |
| [protections-and-limits.md](protections-and-limits.md) | protection ids and thresholds, the alarm word layouts, current-limit calculation, contactor open conditions |
| [parallel-stacks.md](parallel-stacks.md) | up to four stacks on one inverter: election, heartbeat, master aggregation, connect sequencing |
| [registers-via-inverter.md](registers-via-inverter.md) | the battery register block the inverter serves for each stack, and the tunnelled service map |
| [firmware-update.md](firmware-update.md) | how a firmware image travels from the inverter to every stack |

For the inverter side of the battery link see [../sh10rs/battery-interface.md](../sh10rs/battery-interface.md);
for a narrative introduction see [../../../guide/08-inside-the-battery.md](../../../guide/08-inside-the-battery.md).

## Key numbers

| Item | Value |
|---|---|
| Cell chemistry | LFP, 3.2 V nominal |
| Module | 20 cells in series, 64 V, 50 Ah (40 Ah variant configurable), 4 temperature sensors, 2 cell monitors, 1 EEPROM |
| SBR192 stack | 6 modules, 120 cells, 384 V nominal, 50 Ah, 19.2 kWh |
| Voltage limits sent to the inverter | cells × 3.80 V (charge, 456 V for an SBR192), cells × 2.00 V (discharge, 240 V) |
| Stack rated current | 30 A (rated power = modules × 64 V × 30 A) |
| System current cap | 30.0 A for the whole system in the default configuration |
| Modules per stack | up to 8 |
| Stacks per inverter | up to 4 (inverter registry has 5 slots, slaves 200–204) |
| Controller | STM32F417ZG, 25 MHz crystal, 168 MHz |
| Parameters | 632, in SPI FRAM |
| CAN1 (inverter and stacks) | 250 kbit/s, 11-bit ids, little-endian |
| CAN2 (service, stack hand-over) | 250 kbit/s, 29-bit J1939-style ids |
| Cell measurement | every 200 ms; temperatures every 1 s; open-wire test every 6 s |
| Balancing | passive; a pass at most every 240 s; voltage mode after more than 29 min below 0.51 A, bleeding cells 35 mV or more above the lowest until they are within 15 mV of it |
| Cell protection levels | over-voltage 3.65 / 3.70 / 3.80 V; under-voltage 2.70 / 2.50 / 2.00 V |
| Temperature protection | over 56 / 58 / 60 °C; charging below −1 / −3 / −5 °C; discharging below −21 / −23 / −25 °C |
| SOC cut-offs in the BCU | discharge not allowed below 5.00 %; forced-charge request at or below 3.00 % |
| Per-stack telemetry | a block of up to 32 CAN ids (0x700 + 0x20n + k): most once a second, identity frames every 10 s, module serials about once a minute |
| Inverter offline timeout | 20 s per stack |
| BCU inverter-loss alarm | 60 s |
| Firmware staging | 384 KiB internal flash region, CRC-32 checked; signature checked by the inverter |

Register numbers in these pages are the 1-based numbers of Sungrow's Modbus documentation.
