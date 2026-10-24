# SBR protections, alarm words and current limits

The battery control unit (BCU) of an SBR stack supervises every cell and the whole stack with a
table of numbered protections. Each protection has three levels. The levels drive the warning
and protection bit words that the stack sends to the inverter, the current limits, and the
contactors. Register numbers on this page are the 1-based numbers of Sungrow's Modbus
documentation.

## The level model

| Item | Rule |
|---|---|
| Levels | 0 (clear), 1, 2, 3 per protection id |
| Thresholds | each level has its own set threshold, clear threshold and debounce time, all BCU parameters |
| Level 1 | sets a **warning** bit where one exists, and for the voltage, temperature and spread alarms zeroes the matching current limit (charge or discharge) |
| Level 2 and 3 | set **protection** bits; **any protection bit zeroes both current limits** |
| Level 3 | the stack enters the Fault state (limits 0, contactors open) |
| Recovery | Fault returns to Standby after 10 s without a level-3 fault; some faults that persist (3 s to 15 min depending on the fault) move the stack to Shutdown, where it releases its own power latch |
| Counting | every entry into a non-zero level increments a per-id, per-level event counter (a parameter) and writes a fault-log record; the id of the most recent event is readable on the status port ([can-bus.md](can-bus.md#usart3-the-status-slave)) |

## Protection ids and default thresholds

| Id | Quantity | Level 1 | Level 2 | Level 3 | Clears at |
|---|---|---|---|---|---|
| 1 | over-temperature (max cell temperature) | 56 °C | 58 °C | 60 °C | 52 °C |
| 3 | charge under-temperature (min temperature while charging above 0.5 A) | −1 °C | −3 °C | −5 °C | +3 °C |
| 4 | discharge under-temperature (min temperature while discharging) | −21 °C | −23 °C | −25 °C | −17 °C |
| 5 | temperature spread (max − min) | 16 °C | 18 °C | — | 13 °C |
| 7 | cell over-voltage (max cell) | 3.65 V | 3.70 V | 3.80 V | 3.50 / 3.62 / 3.62 V |
| 10 | cell under-voltage (min cell); separate thresholds apply at low temperature | 2.70 V | 2.50 V | 2.00 V | 2.90 / 2.65 / 2.65 V |
| 11 | pack over-voltage (stored defaults for 8 modules) | 584.0 V | 592.0 V | 608.0 V | — |
| 14 | pack under-voltage (stored defaults for 8 modules) | 432 V | 400 V | 320 V | — |
| 15 | cell-voltage spread (max − min cell) | 500 mV | 600 mV | — | 400 mV |
| 18 | over-current, against the module-limited limit + 5 A or the static levels | 35 A | 40 A | 50 A | 25 A |
| 20 | cell-monitor communication loss (continuous PEC failure) | 3 s | 6 s | 10 s | second clean group read |
| 31 | negative stack or link voltage (reverse polarity) | — | — | stack below −30.0 V for 1 s, or link below −30.0 V for 10 s | 1 s non-negative |
| 42 | inverter communication lost (no inverter frame) | — | — | 60 s | next inverter frame |
| 48 | cell-monitor chain probe failed at start-up | — | — | set | restart |
| 53 | pre-charge fault: current keeps flowing while the stack-to-link voltage difference stays large | — | — | set | see lock-out below |

Id 9 (a second under-voltage slot) exists but is never raised. Ids 24, 25, 28–30, 32, 37, 39–41
and 44–46 are set directly by other checks: contactor feedback, cell-monitor and communication
checks, insulation and similar. Their thresholds are not identified, and insulation in
particular has no known threshold. The remaining ids up to 53 have level slots whose triggers
are not identified.

The cell-sampling fault (untrustworthy cell data, [cell-monitoring.md](cell-monitoring.md#cell-sampling-fault))
is not a numbered threshold protection; at level 3 it sets protection bit 22.

In cold weather three things happen in order: the derating table cuts the charge current as the
cells cool; at −1 °C while charging a warning appears, at −3 °C protection bit 3 sets and both
limits go to 0; and voltage-mode balancing has already stopped at 0 °C. Discharge is allowed
down to −21 °C (warning) and −23 °C (protection).

## Alarm words

Each stack sends a 32-bit **protection word** and a 32-bit **warning word** in CAN frame 0x700.
The master's system copy (0x000) combines them over the stacks (see
[parallel-stacks.md](parallel-stacks.md)).

### Protection word

| Bit | Meaning | Inverter code |
|---|---|---|
| 0 / 31 | cell or pack over-voltage, level 2 / level 3 | 732 |
| 1 / 30 | cell or pack under-voltage, level 2 / level 3 | 739 |
| 2 / 29 | over-temperature, level 2 / level 3 | 733 |
| 3 | under-temperature (charge or discharge), level ≥ 2 | 734 |
| 4 / 23 | over-current, level 2 / level 3 | 735 |
| 5 | a second current protection, level 2 | 735 |
| 6 | internal BMS check, level 3 | 741 |
| 17 / 18 | source in the BCU unknown | 840 / 841 |
| 19 | internal BMS check, level 3; in the system word also any stack with a negative-voltage fault above level 2 | not mapped |
| 20 / 21 | internal BMS fault levels, level 3 | 743 / 744 |
| 22 | **cell-sampling fault, level 3** | 745 |
| 24 | **cell-monitor communication loss, level 3** | 746 |
| 27 | contactor fault, level 3 | 747 |
| 28 | internal BMS check, level 3 | 833 |

### Warning word

| Bit | Meaning | Inverter code |
|---|---|---|
| 2 | over-temperature, level 1 | 933 |
| 3 | under-temperature, level 1 | 934 |
| 4 | over-current, level 1 | 935 |
| 5 | the second current protection, level 1 | 935 |
| 6 | **cell-monitor communication loss, level 1 or 2** | 964 |
| 18 | a per-module flag | not mapped |
| 19 | system word only: a stack reports a parallel mismatch (it takes no current although the inverter asks for it, or its voltage is far from the link) | 742 |

The stack builds its warning word only from bits 2–6 and 18. The inverter's code table also
names warning bits 0, 1, 8, 12, 13, 15, 16 and 17 (codes 932, 939, 937, 741, 740, 940, 941,
942) and bit 19 (742). This BCU version never sets bits 0–17 in a stack word; bit 19 appears only
in the master's system word. Cell and pack over- and under-voltage at level 1
therefore act only through the current limits, with no warning bit.

### How the inverter shows them

- **10667–10674** carry the two words as hex text, one byte per register, least significant byte
  first (10667–10670 protection, 10671–10674 warning). Within each register the high character
  is the byte's low nibble, so byte 0x54 reads as "45".
- **5448–5472** per stack (slave 200–204) is a word block: 5449 flags (0x100 fault present, 0x80
  alarm present), 5452 count, then up to ten pairs (code, class) at 5453 + 2i / 5454 + 2i with
  class 25 = fault, 24 = alarm, 0 = unclassified.
- **13070–13079** on the inverter itself (slave 1) are four flag words built from the master's
  system word.
- **10790** is a bitmap of faulted modules: bit m−1 is set for each module named by an active
  voltage or temperature alarm, and for the first module that dropped off the monitor chain.

The codes themselves are listed in [../../appendix/error-codes.md](../../appendix/error-codes.md).

## Current limits

### Per module

The BCU looks up each module's **charge current limit** in a two-dimensional **temperature × SOC
derating table** stored in that module's EEPROM (up to 25 temperature points and 15 SOC points;
flash defaults if the EEPROM cannot be read). The discharge limit comes from a similar
per-module calculation. Modules from different production batches can therefore carry different
tables.

### Per stack

The stack limit is the **lowest module limit**. The BCU then forces it to 0:

| Limit | Forced to 0 when |
|---|---|
| Charge | a module is full; stack SOC = 100.00 %; a cell or pack over-voltage, over-temperature, cell-spread or similar alarm is at level ≥ 1; or any protection bit is set |
| Discharge | a module is empty; stack SOC = 0; a cell or pack under-voltage, over-temperature or similar alarm is at level ≥ 1; or any protection bit is set |

The stack limits go out in the per-stack frame 0x701 (0.1 A), together with voltage limits of
cells × 3.80 V (charge) and cells × 2.00 V (discharge), the level-3 cell set points. The
inverter does not use 0x701; it uses the master's system frame 0x001.

The rated power is modules × 64 V × 30 A (11520 W for an SBR192, register 10696); the maximum
discharge power is modules × 64 V × the module-limited discharge current in whole amps (10697),
so it falls below the rated power when modules derate.

### System limits (master)

The master combines the stacks for frame 0x001. With N the number of stacks counted (in
Running, a stack counts only with its contactor closed) and L the smallest per-stack limit:

| Rule | Limit |
|---|---|
| L = 0 or L > 2.9 A | N × L, capped at 30.0 A (50.0 A in an alternative configuration with more than one stack) |
| 0.1 A ≤ L ≤ 2.9 A | N × 3.0 A |
| a stack is being connected in parallel | at most N × 6.0 A |
| charge while stack rotation is active (off by default) | at most N × 3.0 A |
| low-SOC trickle charge | charge limit at least N × 1.0 A, and at least N × 2.0 A after 600 s with the current below 0.5 A for 30 s |

With two stacks a 30 A discharge limit is the 30.0 A cap, and a 19 A charge limit is 2 × 9.5 A.
Which condition selects the 50.0 A cap is not identified.

### What the inverter does with them

| Register | Value |
|---|---|
| 5635 | system charge limit, A; **forced to 0 when the system SOC is 100.0 %** |
| 5636 | system discharge limit, A; **forced to 0 when the system SOC is 0** |

The inverter applies these limits to its power stage. It adds its own power caps
(33047/33048) but no battery logic.

## Request flags (10666)

The second byte of frame 0x705, served as 10666:

| Bit | Meaning |
|---|---|
| 0 | charging not allowed (charge limit 0) |
| 1 | discharging not allowed (discharge limit 0, SOC below the discharge cut-off SOC, link down, …) |
| 2 | forced-charge request (at or below the emergency-charging SOC, or a low cell while running) |
| 4 | mode-dependent, meaning not identified |

The thresholds are in [balancing-and-soc.md](balancing-and-soc.md#soc-window-from-the-inverter).
The system copy in 0x005 also carries the master's trickle-charge and join-charge requests.

## Contactor open conditions

| Cause | Path |
|---|---|
| any level-3 protection | Fault state: limits 0, contactors open |
| Standby, Shutdown, Update and Test states | the state's entry action opens the contactors |
| firmware update, or a CAN2 service-bus record export, started | Update state ([firmware-update.md](firmware-update.md)); in the update state, current above 10 A for 10 s or an alarm above level 1 also switches the outputs off |
| master's disconnect command | parallel rotation, off by default ([parallel-stacks.md](parallel-stacks.md)) |
| cold connect lock | after 72 h at rest followed by 6 h at or below 0 °C, Running opens and Standby refuses to close until the cells are above 5.0 °C for 15 min |
| inverter stop request | inverter mode 3 in frame 0x100, honoured only when a parameter enables it (default off) |
| service command | CAN2 control group or tool map address 0x2000: open (0xBB), close (0xAA), reset |
| BCU reset or shutdown | outputs off in order, then reset; refused while more than a small current flows and the contactors are active |

### Permanent lock-out

During pre-charge, if the stack-to-link voltage difference stays large while at least 0.80 A
keeps flowing for more than five checks, the BCU raises protection 53 at level 3. If the current
continues for another 3 s it writes a lock-out value into a non-volatile parameter and releases
its power latch. While the value is set, protection 53 is held at level 3 on every pass, the
status port reports Fault, and the BCU powers itself down every 3 s; this survives power cycles.
A welded contactor or a short are plausible causes. Only the generic parameter writers (the CAN2
parameter service, the indirect parameter write in the service map, or a restore of defaults)
clear it. A board-variant strap disables the lock-out.

## Fault log

Every protection level change writes a **fault-log** record (kind 3) to the record storage (SD
card or on-board NAND). A separate 164-byte **fault-upload** record (kind 4) captures a snapshot
for the cloud. Run-log (kind 1) and event-log (kind 2) records sit alongside; every service write
and every USART3 start request is an event record. The inverter pulls all four kinds through the
0x1D0 record export when the dongle requests a battery log ([can-bus.md](can-bus.md#the-0x1d0-record-export)).
The record payload layouts are not decoded.

## Not yet known

- How the eight-module pack over- and under-voltage defaults apply to a six-module stack.
- The BCU-side source of protection bits 17 and 18 and the exact checks behind bits 6, 19, 20,
  21 and 28.
- The thresholds of the protections set directly by other checks, including insulation.
- The condition that raises the system current cap from 30.0 A to 50.0 A.
