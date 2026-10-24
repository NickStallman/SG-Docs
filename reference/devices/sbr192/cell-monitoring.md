# SBR cell monitoring and the module EEPROM

Every cell voltage and module temperature that an SBR stack reports comes from a daisy chain of
cell-monitor chips inside the modules, read by the battery control unit (BCU) over isoSPI. The
same chain gives access to a small EEPROM in each module. This link is internal to the battery:
neither the inverter nor the dongle sees it.

## Chain and chips

| Item | Value |
|---|---|
| Chips per module | 2, each measuring 10 cells and 2 thermistors, so 20 cells and 4 temperatures per module |
| Chain length | 2 × module count, up to 16 chips for 8 modules; module 1 is nearest the BCU |
| Command set | the Analog Devices LTC6811/LTC6813 opcodes (WRCFGA, RDCVA–D, RDAUXA/B, ADCV, ADOW, ADAXD, CLRCELL, PLADC, MUTE, UNMUTE, WRPWM, RDPWM, WRCOMM, STCOMM, RDCOMM) |
| Chip generation | every register read or write carries a **10-bit data PEC plus a 6-bit command counter**, and the I2C COMM bytes use the [ICOM\|FCOM][data] layout. Both are features of Analog Devices' second-generation monitors (ADBMS68xx class); the exact part number is not identified. The configuration register layout is not the LTC6811 one |
| BCU side | SPI2 at 656.25 kHz, SPI mode 3, software chip select, DMA; an isoSPI transceiver (type not identified) converts to the transformer link |
| Addressing | none: every command is broadcast. A read returns 8 bytes per chip in chain order |
| Wake-up | before each command the BCU sends one chip-select pulse with a dummy byte per chip so every chip leaves its idle state |

### Frame integrity

| Part | Check |
|---|---|
| Command | 2 command bytes followed by the standard PEC15 (polynomial 0x4599, seed 0x0010) |
| Register data | 6 data bytes, then byte 6 = command counter (bits 7–2) and PEC bits 9–8, byte 7 = PEC bits 7–0. The PEC is a CRC-10 (polynomial 0x08F, seed 0x010) over the 6 data bytes and the 6 counter bits |
| Writes | the counter bits are sent as zero |
| Reads | a chip's data is accepted only if the PEC matches; the command counter is stored but not checked |

## Commands in use

| Command | Opcode | Use |
|---|---|---|
| WRCFGA | 0x0001 | rewrites the configuration (`80 00 2D 5F 00 00` by default) at the start of every cell pass |
| RDCFGA | 0x0002 | start-up chain probe; read-back of the GPIO levels |
| ADCV | 0x0360 | cell conversion, all cells, normal ADC mode, discharge not permitted during the conversion |
| RDCVA–RDCVD | 0x0004, 0x0006, 0x0008, 0x000A | cell read-back, groups 1–4 (12 words per chip, 10 used) |
| ADAXD | 0x0500 | auxiliary conversion of all GPIOs and the second reference |
| RDAUXA, RDAUXB | 0x000C, 0x000E | auxiliary read-back (GPIO1–5 and the reference) |
| CLRCELL | 0x0711 | clear the cell registers before the open-wire test |
| ADOW | 0x0368 / 0x0328 | open-wire conversion with pull-up / pull-down current |
| PLADC | 0x0714 | poll until a conversion is done (at most 2000 polls) |
| MUTE / UNMUTE | 0x0028 / 0x0029 | suspend and resume cell discharge around each measurement |
| WRPWM / RDPWM | 0x0020 / 0x0022 | write and read back the per-cell discharge duty (balancing) |
| WRCOMM, STCOMM, RDCOMM | 0x0721, 0x0723, 0x0722 | drive each chip's I2C master to reach the module EEPROM |

The discharge switches are not in the configuration register: balancing uses the PWM registers,
twelve 4-bit duty values per chip (0xF = full duty). **MUTE and UNMUTE bracket every cell and
open-wire measurement** so that the balancing current does not disturb the readings.

## Measurement cycle

The measurement task runs every 200 ms with a counter that counts 0 to 899 and wraps (180 s).

| Block | When | Steps |
|---|---|---|
| Cell pass | every cycle (200 ms) | MUTE, wait 10 ms, WRCFGA, mark all cell words invalid (0xFFFF), ADCV, wait 6 ms, RDCVA–RDCVD with 3 ms after each group, copy the cells, UNMUTE |
| Temperature pass | every 5th cycle (1 s) | ADAXD, wait 6 ms, RDAUXA and RDAUXB with 3 ms after each, convert. Not inside MUTE/UNMUTE |
| Open-wire test | every 30th cycle (6 s), after that cycle's cell pass, never at counter 0 | MUTE, wait 10 ms, CLRCELL, ADOW pull-up, PLADC, read 4 groups, ADOW pull-down, PLADC, read 4 groups, evaluate, UNMUTE |
| Balancing write | on request from the balancing logic | WRPWM, 1 ms, RDPWM, compare all 6 bytes per chip; a mismatch or PEC error counts as a failed write |

Cell voltages are kept as unsigned 16-bit values in 0.1 mV. The monitors are not polled at all
during a firmware update, in Test mode while a service tool injects values, and after the
deep-discharge latch (below).

### Temperatures

Each chip has its divider reference on GPIO1 and its two thermistors on GPIO2 and GPIO3. The
ratio G × 1000 / (G1 − G) is looked up in a 166-point NTC table running from −40 °C in 1 °C
steps; the result is in 0.01 °C. An invalid reading, or an auxiliary group with a PEC error,
gives 0x7FFF. A module therefore has four temperatures, numbered sensors 1–4 in the
temperature location registers (10762, 10764).

## Open-wire detection

For each cell the BCU takes the pull-up reading minus the pull-down reading (when the pull-down
reading is the smaller one). A cell is flagged when the difference exceeds a threshold:

| Item | Value |
|---|---|
| Threshold, first and last cell of a chip | 400.0 mV (configurable, 50.0–3000.0 mV; service map 19711) |
| Threshold, other cells | 400.0 mV (configurable; service map 19713) |
| Counter | consecutive detections per cell |
| Module open-wire level | 1 after one detection, 2 after two or three, 3 after four or more (about 18–24 s) |
| Result | a 24-bit flagged-cell map and the first flagged cell per module |

The criterion flags the cell whose upper sense wire is open; an open bottom wire of a chip's
first cell cannot be seen. The module open-wire levels feed the cell-sampling fault below.

## Fault levels

### Communication loss (PEC failures)

After every group read the BCU counts chips whose PEC failed.

| Condition | Effect |
|---|---|
| failures continue for 3 s | communication-loss level 1; **all 160 cell words are set invalid (0xFFFF) and all 32 temperature words to 0x7FFF** |
| for 6 s | level 2 |
| for 10 s | level 3 |
| one clean group read | the failure timer restarts; with the default clear times (0 ms) the level returns to 0 at the second clean group read |

Because one clean group resets the timer, the level rises only when every group read in a row
fails. The number of the first module that failed is kept; it sets that module's bit in the
faulted-module bitmap (10790), and balancing is stopped on that module and every module behind
it in the chain.

The level is mirrored into protection id 20 (so each change is counted and written to the fault
log), appears as **warning bit 6** at levels 1–2 and **protection bit 24** at level 3, **blocks
balancing at level 3**, and is taken into account by the current-limit calculation.

### Cell-sampling fault

A second level summarises whether the cell data can be trusted.

| Condition | Effect |
|---|---|
| any module open-wire level | taken over at once |
| some cell word invalid for 3 s / 6 s / 10 s | level 1 / 2 / 3 |
| neither condition for 3 s | cleared; the clear also resets every module's open-wire level |

Balancing requires this level to be 0. At level 3 it sets **protection bit 22** (inverter fault
code 745), which zeroes both current limits. It also gates the deep-discharge timer.

### Chain probe at start-up

At start-up the BCU reads the configuration of 16 chips five times in a row and counts the
chips whose PEC is valid; it needs the same count five times. The module count becomes
(count + 1) / 2 and replaces the stored count if it differs (after a plausibility check of the
summed cell voltage against the measured stack voltage, within 30.0 V). If five attempts fail,
protection id 48 is set at level 3 and the module count is set to 1.

## Deep-discharge latch

The core BMS task runs a one-hour timer to stop the battery draining itself after a deep
discharge.

| Item | Rule |
|---|---|
| Timer runs while | the average cell voltage is below 2.000 V; or the average is at or above 2.000 V, the minimum cell is below 1.700 V and there is no cell-sampling fault |
| Timer resets | on any other state |
| After one hour | the BCU stops all balancing, stops polling the cell monitors and sets a latched flag |
| While latched | no cell, open-wire or temperature passes; the 1 s SOC/SOH aggregation and stack checks are skipped |
| Cleared by | a restart only |

The purpose is probably to cut the stack's self-consumption (monitor chain and balancing) once the cells
are deeply discharged. The latch state is visible in one of the CAN2 telemetry frames.

## Module EEPROM

| Item | Value |
|---|---|
| Location | one I2C EEPROM per module, on the I2C master of the module's second monitor chip |
| Bus addressing | slave address 0xA0 (write) / 0xA1 (read), 16-bit memory address, 32-byte pages |
| Size | at least 8 KB (24C64 class) |
| Storage format | **every logical byte is stored twice, as the byte followed by its complement**; the physical address is 2 × the logical offset. Every read verifies that each pair XORs to 0xFF |
| Parallel access | all chips receive the same COMM data, so one read fetches the same address from every module's EEPROM at once |
| Write select | a chip GPIO (configuration byte 3, bit 5) acts as write protect. To write one module, the BCU releases the protect on that module's chip and asserts it on all others |
| Read failure | a failed complement check falls back to defaults stored in the BCU flash; the cell-type byte is then reported as 1 |

### Contents (logical offsets)

| Offset | Size | Content |
|---|---|---|
| 0x000 | 0x26 bytes | 18-character module serial number. Character 5 is the **cell-type code**, characters 6–11 encode the manufacturing date |
| 0x028 | 0xA8 bytes | table geometry (up to 25 temperature points and 15 SOC points) and per-module scalars |
| 0x140 | | **charge-current derating table**, temperature × SOC; further tables at 0x280, 0x3C0, 0x500, 0x640, 0x780 and 0x8C0 |
| 0xA00 | 0x3E bytes | persisted **statistics block**: the module's SOC model state, cycle counter and related values. Written back when the stack SOC has moved by more than 3 % and the values pass a range check |

The module date code served as 10791–10806 is computed from serial characters 6–11 (0-based):
10⁶ × (c7c8) + 10⁴ × month + 100 × (c10c11) + (c6), where c9 is the month as a hex digit (A–C =
October to December). Characters 6–11 = `424B15` give 24111504, that is 2024-11-15. The code
is 0xFFFFFFFF if any of those characters is below '0'.

The EEPROMs are also reachable from outside the battery: the tool map on USART1 and CAN id
0x5F0 has read/write windows for each table (no check on writes), the CAN2 export can dump a
module image, and the service map has a selector at 19715 plus a 4-byte write at 19717 (see
[can-bus.md](can-bus.md) and [registers-via-inverter.md](registers-via-inverter.md)). After a
write outside the statistics area the BCU reloads all module information.

## Where the data goes

| Data | Path |
|---|---|
| Cell voltages | stack extremes and locations (0x714 → 10757–10760), per-module min/max (0x715–0x718 → 10765–10780), CAN2 bulk arrays |
| Temperatures | stack extremes (0x713 → 10761–10764), stack temperature (0x704 → 10743) |
| Module serial, cell type, date | 0x71F → 10822–10893, 0x71A → 10781–10788, 0x71B–0x71E → 10791–10806 |
| Derating tables | charge and discharge current limits ([protections-and-limits.md](protections-and-limits.md)) |
| Fault levels | protection and warning words, faulted-module bitmap (10790) |

Register numbers on this page are the 1-based numbers of Sungrow's Modbus documentation.
