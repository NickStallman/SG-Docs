# SBR registers served by the inverter

An SBR stack has no Modbus port that the dongle can reach. The SH10RS hybrid inverter builds a
battery register block for each stack from the CAN frames it receives and answers Modbus reads
for **slaves 200–204** itself, from RAM; the battery is not asked. Only the service window
**19101–20000** is forwarded to the battery control unit (BCU), through the CAN 0x1E0 tunnel.

All register numbers on this page are the 1-based numbers of Sungrow's Modbus documentation;
the address on the wire is the number minus 1. Battery registers are input registers
(function 4). u32 values are two registers, **low word first**. The generated full table is
[../../appendix/register-tables/rs485-battery.md](../../appendix/register-tables/rs485-battery.md).

## How the slaves appear

| Item | Behaviour |
|---|---|
| Binding | slave 200 + k is the stack whose serial number took slot k of the inverter's 5-slot registry, in first-seen order; the binding survives reboots ([parallel-stacks.md](parallel-stacks.md#what-the-inverter-sees)) |
| Offline | 20 s without a frame from a stack unbinds its slave; it stops answering until the stack registers again |
| Refresh | values change at most once a second. The identity group (10665, 10699, 10701, 10705, 10711–10735) refreshes every 10 s, the module serial numbers about every 61 s |
| Freeze | during a battery log export (CAN 0x1D0) the inverter parses no battery frames, so every value holds still |
| Unimplemented | inside 5200–12000, any address the inverter does not implement reads 0xFFFF |

## Not served (0xFFFF)

| Registers | Note |
|---|---|
| 10601–10664 | the whole range reads 0xFFFF; no BCU data exists for it. Readers that type rows here as s16/s32 see −1, strings read empty |
| 10752–10756 | 0xFFFF on the battery slaves. On slave 1 the same numbers are the inverter's maintenance-procedure status |
| 10807–10820 | 0xFFFF; 10821 reads a constant 0 |
| 10894 and above | 0xFFFF |

## Served registers

"CAN" names the per-stack frame for stack 0 (add 0x20·n for stack n) and its payload words
(w0–w3) or bytes (b0–b7). Mux refers to the multiplexed statistics frame 0x70F
([can-bus.md](can-bus.md#0x70f-multiplexed-statistics)).

| Register | Type | Scale, unit | Meaning | CAN |
|---|---|---|---|---|
| 5448–5472 | words | — | per-stack fault list: 5449 flags (0x100 fault, 0x80 alarm), 5452 count, then up to ten (code, class) pairs at 5453 + 2i / 5454 + 2i; class 25 fault, 24 alarm | 0x700 |
| 10665 | u16 | 0.01 kWh | stack nominal energy (1920 = 19.20 kWh); the inverter sums it into 5639 | 0x707 nominal energy / 10 |
| 10666 | u16 | bits | request flags: bit 0 charging not allowed, bit 1 discharging not allowed, bit 2 forced-charge request, bit 4 mode-dependent | 0x705 b1 |
| 10667–10674 | 8 × 2 chars | hex text | protection word (10667–10670) and warning word (10671–10674), one byte per register, least significant byte first, low nibble as the first character | 0x700 |
| 10675–10682 | u16 | 0.01 % | module 1–8 SOC; 0xFFFF for absent modules | mux 5–7 |
| 10683 | u16 | enum | balancing: 0 idle, 1 capacity mode, 2 voltage mode | mux 4 b7 |
| 10684 | u16 | enum | low-SOC rest calibration: 0 inactive, 1 some modules calibrated, 2 reserved, 5 all modules calibrated | mux 4 b6 |
| 10685/10686 | u32 | 0.1 cycle | cycle count (module 1's counter) | mux 4 b2–b5 |
| 10687 | u16 | 0.01 % | lifetime discharge Ah / charge Ah; 9990 marks a ratio ≥ 1.0007 | mux 3 b6b7 |
| 10688/10689 | u32 | Ah | lifetime discharge throughput | mux 3 b2–b5 |
| 10690 | u16 | 0.1 mΩ | internal resistance model: cells × (40.00 + 0.005 × cycles) | mux 2 b6b7 |
| 10691/10692 | u32 | Ah | lifetime charge throughput | mux 2 b2–b5 |
| 10693 | u16 | count | deep-discharge count: entries into cell under-voltage level 2 (min cell ≤ 2.50 V) | mux 1 b6b7 |
| 10694 | u16 | h | time charging (above 0.5 A) under an under-temperature alarm at level ≥ 2 | mux 1 b4b5 |
| 10695 | u16 | h | time under an under-temperature alarm at level ≥ 2 | mux 1 b2b3 |
| 10696 | u16 | W | rated power = modules × 64 V × 30 A (11520 for an SBR192) | mux 0 b6b7 |
| 10697 | u16 | W | max discharge power = modules × 64 V × module-limited discharge current; below 10696 when modules derate | mux 0 b4b5 |
| 10698 | u16 | 0.01 Ah | rated capacity: 5000, or 4000 for the 40 Ah variant | mux 0 b2b3 |
| 10699/10700 | u32 | — | BMS capability word, constant 15 (below) | 0x70D b0–b3 |
| 10701 | u16 | bitmask | constant 15: probably the app's mask of read-write groups (1 = the SOC cut-off pair 19939/19941) | 0x70E b2b3 |
| 10702–10704 | u16 | — | 0 | — |
| 10705 | u16 | bitmask | constant 7: probably the app's mask of read-only groups (1 basic information, 2 contactor and module data, 4 life and chemistry) | 0x70E b0b1 |
| 10706–10708 | u16 | — | 0 | — |
| 10709 | u16 | flag | stack online: 1 after the serial-number frame | inverter |
| 10710 | u16 | — | model code 0x20E6 + (modules − 2): 0x20EA = 6 modules = SBR192 | 0x705 b3b4 |
| 10711–10720 | string | 20 chars | stack serial number | 0x708 |
| 10721–10735 | string | 30 chars | BCU firmware version `SBRBCU-S_22011.xx.yy`, built from bytes 3 and 0 of the version word (0x01000030 gives 01.30) | 0x707 |
| 10736/10737 | u32 | — | battery protocol code "10AA" (0x41413130); an inverter constant | inverter |
| 10738/10739 | u32 | — | battery protocol version 0x01000800 = V1.0.8; an inverter constant | inverter |
| 10740 | u16 | enum | operating state: 0 Idle, 1 Standby, 2 Running, 3 Fault, 4 Shutdown, 5 Update, 6 Correction, 7 Test; 0x7FFF = offline (set by the inverter) | 0x705 b0 |
| 10741 | u16 | 0.1 V | stack voltage | 0x704 w0 |
| 10742 | s16 | 0.1 A | stack current, **positive = discharge** | 0x704 w1 |
| 10743 | s16 | 0.1 °C | stack temperature | 0x704 w3 |
| 10744 | u16 | 0.1 % | stack SOC | 0x702 w0 / 10 |
| 10745 | u16 | 1 % | stack SOH (lowest module) | 0x702 w1 / 100 |
| 10746/10747 | u32 | 0.1 kWh | lifetime charge energy | 0x703 b0–b3 / 100 |
| 10748/10749 | u32 | 0.1 kWh | lifetime discharge energy | 0x703 b4–b7 / 100 |
| 10750 | u16 | — | the **inverter's** own Modbus address | inverter |
| 10751 | u16 | — | the **inverter's** model class (3 or 4, from its device type) | inverter |
| 10757 | u16 | 0.1 mV | highest cell voltage | 0x714 w1 |
| 10758 | u16 | location | its location: module in the high byte, cell in the low byte (0x0310 = module 3, cell 16) | 0x714 w0 |
| 10759 | u16 | 0.1 mV | lowest cell voltage | 0x714 w3 |
| 10760 | u16 | location | its location | 0x714 w2 |
| 10761 | s16 | 0.1 °C | highest temperature | 0x713 w3 |
| 10762 | u16 | location | its location: module in the high byte, sensor 1–4 in the low byte | 0x713 w2 |
| 10763 | s16 | 0.1 °C | lowest temperature | 0x713 w1 |
| 10764 | u16 | location | its location | 0x713 w0 |
| 10765–10772 | u16 | 0.1 mV | module 1–8 highest cell voltage | 0x715–0x718, second word per module |
| 10773–10780 | u16 | 0.1 mV | module 1–8 lowest cell voltage (0xFFFF without a valid cell) | 0x715–0x718, first word per module |
| 10781–10788 | u16 | ASCII | module 1–8 cell-type code = module serial character 5; 1 if the module EEPROM could not be read, 0 if there is no module | 0x71A byte m |
| 10789 | u16 | enum | DC contactor: 0 open, 1 fault, 2 closed, 3 fault | 0x719 b0 |
| 10790 | u16 | bitmap | faulted modules: bit m−1 = module m (voltage or temperature alarm, or dropped off the monitor chain); 0 = none | 0x719 b1 |
| 10791–10806 | u32 × 8 | date code | module 1–8 manufacturing date: YY × 10⁶ + month × 10⁴ + day × 100 + serial character 6 ([cell-monitoring.md](cell-monitoring.md#contents-logical-offsets)) | 0x71B–0x71E |
| 10822–10893 | string × 8 | 18 chars | module 1–8 serial number, 9 registers each | 0x71F |
| 19939/19940 | u32 | 0.01 % | discharge cut-off SOC (default 5.00 %), through the tunnel | 0x1E0 |

The per-stack frames 0x701 (stack limits), 0x706 (extremes in mV), 0x709–0x70C and 0x710–0x712
reach no battery register. The stack limits reach the inverter only through the master's system
frame 0x001, as inverter registers 5635/5636.

## The capability word (10699)

Frame 0x70D carries the constant bytes `0F 00 00 00 0F 00 00 00`. The inverter keeps the first
u32, so 10699/10700 reads 15. The meaning of the bits is unknown; a BMS that sends a different
value would help identify them. The BCU does not send 0x70D while a firmware-update session is
open. 10701 and 10705 come from the constant frame 0x70E and are 16-bit numbers; readers that
type 10701–10704 or 10705–10708 as strings see empty strings because the first byte is 0.

## Quirks

- **Second-word reads of u32 values.** At 10699 the inverter emits four bytes and skips 10700, so
  a read that starts at 10700 gets 0xFFFF. In 10791–10806 the first register of each pair emits
  four bytes and the second emits none, so a read that starts on a second register gets a short
  reply. Always start a read on the first register of a pair.
- **Fault list is not text.** 5448–5472 is a word block whose first word is always 0, so a reader
  that treats it as a string sees nothing.
- **Hex text with swapped nibbles.** To decode 10667–10674, swap the two characters of each
  register, parse them as hex and assemble the bytes little-endian.
- **Inverter total charge truncation.** On the inverter itself (slave 1), 5673 serves only the
  low 16 bits of the total battery charge energy and 5674 is never written; the full u32 is at
  13041/13042. Totals above 6553.5 kWh therefore wrap at 5673.
- **Tunnel function codes.** The 19101–20000 tunnel accepts functions 3, 4 and 16. A
  single-register write (function 6) gets no reply; write one register with function 16 instead.
  A write to an address in the window that no handler names is acknowledged and does nothing.
- **Stale read on the tool map.** On the BCU's own tool map (USART1 / CAN 0x5F0, not reachable
  through the inverter), the write-only register 0x4EE9 reads back the value of 0x4EE8.
- **Slave 1 in the tunnel window.** A request for 19101–20000 addressed to the inverter's own
  address goes to the stack in slot 0.

## The service map (19101 to 20000)

The inverter forwards requests for 19101–20000 on slaves 200–204 to the matching BCU over CAN
0x1E0, rewriting the slave byte to the BCU's own address. If the slave is unbound there is no
reply at all. The BCU decodes this window by **wire address** and holds **one u32 per even wire
address**, low word first, so the useful 1-based numbers are odd. The table gives both.

| Register (1-based) | Wire | Access | Content |
|---|---|---|---|
| 19101–19108 | 19100–19107 | read, write | four scratch cells. 19105 and 19107 hold the start and end of the record-export time window (BCD YYMMDDHH, [can-bus.md](can-bus.md#the-0x1d0-record-export)); a write to 19105 also resets the export status |
| 19701–19758 | 19700–19757 | write | commands: counter and state resets, a capacity value (19709), the two open-wire thresholds (19711, 19713; default 400.0 mV), a selector (19715: module, length, offset) and a data register (19717). With a module selected, 19717 writes up to 4 bytes of that module's EEPROM; with module 0 it writes the selected BCU parameter |
| 19721–19757 | 19720–19756 | read | parameter window 1: 19 BCU parameters, one per u32 |
| 19759/19760 | 19758 | write | **module SOC write**: sets every module's SOC to the u32 value in 0.001 % (100000 = 100.000 %). The SH10RS sends it once per charging episode when the master reports seven modules ([balancing-and-soc.md](balancing-and-soc.md#calibration-at-the-top-and-between-stacks)) |
| 19801–19804 | 19800–19802 | read | a 64-bit value, meaning not identified |
| 19805 | 19804 | read | not identified |
| 19807 | 19806 | read | highest-cell location as module × 100 + cell |
| 19809 | 19808 | read | highest cell voltage |
| 19811 | 19810 | read | lowest-cell location as module × 100 + cell |
| 19813 | 19812 | read | lowest cell voltage |
| 19815 | 19814 | read | contactor status (as 10789) |
| 19817 | 19816 | read | faulted-module bitmap (as 10790) |
| 19819 | 19818 | read | status bits, not identified |
| 19821, 19823 | 19820, 19822 | read | two capacity values, 0.01 Ah |
| 19825 | 19824 | read | rated capacity |
| 19827, 19829 | 19826, 19828 | read | lifetime charge and discharge, 0.01 Ah |
| 19831–19846 | 19830–19845 | read | module 1–8 highest cell voltage |
| 19849–19864 | 19848–19863 | read | module 1–8 lowest cell voltage |
| 19867–19882 | 19866–19881 | read | a per-module value, not identified |
| 19883–19898 | 19882–19897 | read | module 1–8 SOC |
| 19899 | 19898 | read | **indirect read** of the item selected through 19715/19717: the selected parameter or EEPROM bytes, or a diagnostic code such as 0x1000 (calibration aggregate), 0x1001 (balancing state), 0x1004 (per-module calibration states as nibbles), 0x1009 onwards (per-module Ah pairs), 0x101C (board-variant code), 0x1028–0x102C (record-export status) |
| 19939/19940 | 19938 | read, write | **discharge cut-off SOC**, 0.01 %, default 500 = 5.00 %, range 0–10000 |
| 19941/19942 | 19940 | read, write | **emergency-charging SOC**, 0.01 %, default 300 = 3.00 % |
| 19943/19944 | 19942 | read, write | the third parameter of this window, meaning not identified |

The dongle's battery poll reads 19939. The iSolarCloud app lists 19939 and 19941 as holding
registers writable at its senior access level, with 19939 limited to 0–5000 and required to be at
least 19941. Writing 33061 = 0xAA on the inverter (maintenance procedure 1) sets both SOC
thresholds to 0 for the duration and restores the factory values 5.00 % and 3.00 % afterwards.

The window has no authentication beyond what the inverter applies to slave 200–204 writes. The
indirect write reaches every BCU parameter and every module EEPROM, including the non-volatile
lock-out described in [protections-and-limits.md](protections-and-limits.md#permanent-lock-out).
Every tunnel frame counts as inverter contact for the BCU's 60 s communication-loss timer. The
inverter blocks this tunnel while it relays a BCU firmware update, and a tunnel request sent during
a record export can get no answer.

## Related inverter registers (slave 1)

These are served by the inverter for the whole battery system, from the master's system frames.

| Register | Meaning |
|---|---|
| 5630–5632 | battery voltage, current (positive = discharge) and temperature measured by the inverter |
| 5633 / 5634 | system SOC and SOH, 0.1 % |
| 5635 / 5636 | system charge / discharge current limit, A |
| 5639 | battery capacity, 0.01 kWh: the sum of the stacks' 10665 (or the override in 33049) |
| 5670/5671 | total battery discharge energy, 0.1 kWh |
| 13039 | system nominal energy, 0.1 kWh |
| 13041/13042 | total battery charge energy, 0.1 kWh (full value) |
| 13058 / 13059 | max / min SOC sent to the battery |
| 13070–13079 | flag words built from the system alarm word |
| 10752–10756, 33060–33063 | maintenance-procedure status and control |

The full list is in [../sh10rs/battery-interface.md](../sh10rs/battery-interface.md).
