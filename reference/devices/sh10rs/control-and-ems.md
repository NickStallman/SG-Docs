# SH10RS control and energy management

This page covers the writable side of the SH10RS:

- start/stop;
- the energy-management (EMS) mode and forced charge/discharge;
- the battery SOC window and backup reserve;
- export limitation and battery power caps;
- the other hybrid settings the dongle's web UI shows.

It also covers how the inverter validates a write, and what the dongle and iSolarCloud expose of
these settings. Grid-code and protection settings are on
[grid-codes-and-drm.md](grid-codes-and-drm.md).

> **Numbering.** Register numbers are 1-based, as in Sungrow's documents; the PDU address is one
> less. Everything on this page is a **holding register**: read with function code 3, write with
> function code 6 (one register) or 16 (several). Live values quoted are from a hybrid site
> (SH10RS with two SBR192 stacks) running in self-consumption mode.

## How the inverter validates a write

The SH10RS checks every write itself, whoever sends it.

| Case | Inverter response |
|---|---|
| Register accepted, value valid | normal echo (function 6: address and value; function 16: address and count) |
| Register does not apply to this device type | exception **05** |
| Value outside the register's range or set, or the write is refused in the current state | exception **04** |
| Example of a state refusal: DRM enable (30113) while the EMS mode is 8 | exception 04 |
| Settings in 30000–36000 | checked twice: by the controller's per-register rule set (ranges and cross-register relations, not documented; the dongle's web UI enforces its own copy, see [grid-codes-and-drm.md](grid-codes-and-drm.md)), and then by a fixed acceptance range per register inside the power stage, which drops an out-of-range value silently while the write is still acknowledged |
| 19101–20000 | forwarded to the battery control unit over CAN (function codes 3, 4 and 16; a function-6 write is forwarded but never answered, so it times out) |

**Read-back.** A value accepted in the 13000–13370 window is stored before the echo is sent and
served from the same field, so a read straight after the echo returns the new value. 13051 reads
back as written in every EMS mode; 13052 and 13074 are kept in 10 W steps and read back rounded
down to a multiple of 10 W.

A few address windows are internal channels of the inverter rather than settings. Do not write
to them from a client:

| Window | What it is |
|---|---|
| 16000–17500, 29500–29899, 36702–38999 | forwarded to the power-stage DSP; each request stalls the inverter's other ports for at least 1.5 s |
| 19101–20000 | tunnelled to the battery control unit |
| 20000–20175 | firmware-update mailbox |
| 21000–21018 | temporary RS-485 baud-rate switch |
| 30456–30460 | persistent RS-485 line settings and slave address |
| 36099 | enable for the raw DSP relay at address 0xA5 |
| 37004 | DSP-forward hold mode (0xAA holds the other ports indefinitely) |

See [register-windows.md](register-windows.md) for the full window map.

## Clock (5000–5005)

| Reg | Meaning | Range |
|---|---|---|
| 5000 | year | e.g. 2026 |
| 5001 | month | 1–12 |
| 5002 | day | 1–31 |
| 5003 | hour | 0–23 |
| 5004 | minute | 0–59 |
| 5005 | second | 0–59 |

Write all six in one function-16 frame, in local time including daylight saving (the dongles themselves write standard time without daylight saving). The inverter
uses this clock for its daily counters, its time-of-use windows and the time it sends to the
battery (CAN 0x101).

## Start and stop

| Reg | Meaning | Values | Live |
|---|---|---|---|
| 5006 | power switch | 0xCF (207) = start, 0xCE (206) = stop | 0xCF |
| 13000 | start/stop in the published map | same values; mirrors 5006 | 0xCF |

5006 is the register the dongle uses for this inverter. It appears on the "General" web-UI
parameter page as `power_switch`, labelled "Start up / Shutdown". 13000 reads the same value. On
protocol-type-5 hybrids, 13000 is the primary start/stop register.

Stop takes the inverter off the grid, but not at once: the power stage limits active power to
1 %, ramps down until the output is below 6.25 % of rated power (or a ramp timer runs out), then
opens the relays and reports state 0x1300. The value 0xBB (187) is an emergency stop, reported as
0x1500. Start (0xCF) is accepted in any stopped state; the power stage then waits for the
reconnection delay (30091) with the grid within limits before it starts, see
[Operating states](#operating-states). The command is reversible. A DRM0 command from a DRED
raises the same stop request inside the controller. The General web-UI page also carries a
"Restore default" setting at 30050 (85 when idle). It is not a start/stop control.

Holding 5007 (power-limitation switch, 0xAA/0x55) and 5008 (power-limitation setting, 0.1 %)
also exist on the SH10RS (live 0xAA and 1000). They are the string-inverter output-limit
controls.

## Operating states

The power stage runs a small state machine; the state register (5221, 5038, 13000) reports it.

| State | Code | Leaves when |
|---|---|---|
| Initial standby | 0x1200 | the controller has pushed its parameters and a PV input, a battery or a valid grid is present (about 10 s after power-up); without parameters for 8 s it stops |
| Standby | 0x1400 | the start conditions have held for the reconnection delay: grid within limits, a PV input above its start voltage or a battery available, relay and sensor self-tests passed, and the controller link alive |
| Starting | 0x1600 | a 15-step sequence completes: bus voltage check, battery pre-charge, PV boost start, relay close, grid synchronisation; any step failure returns to Standby, and a relay that does not close raises fault 38 |
| Running | 0x0000 and the running sub-codes | a stop command (ramp-down, then 0x1300), a grid-side trip (back to Standby), the low-power timer at night (back to Standby) or a fault |
| Fault | 0x5500 | the grid has been within limits for the recovery time (30092); alarm-class bits clear themselves after 10 min when running or 10 s when stopped. With automatic fault recovery (30430) off the trip flags are held until a stop command |
| Stopped | 0x1300 / 0x1500 | a start command (0xCF) |

The reconnection delay is 30091 seconds, extended by 10 minutes after each of the first three
grid-side trips and to one hour after that; the extension is counted only while the grid is within
limits and resets after three minutes of running. Losing the link to the controller for about 100 s
blocks a restart but does not stop a running inverter.

## EMS mode and forced charge/discharge

| Reg | Meaning | Type, scale | Values / range | Live |
|---|---|---|---|---|
| 13050 | EMS (work) mode | u16 enum | 0 self-consumption (default), 2 forced mode, 3 external EMS, 4 VPP, 8 MicroGrid, 9 scheduled forced mode | 0 |
| 13051 | forced charge/discharge command | u16 enum | 0xAA (170) charge, 0xBB (187) discharge, 0xCC (204) stop | 0xCC |
| 13052 | forced charge/discharge power | u16, 1 W | 0–10600 accepted (106 % of the rating, for charge and discharge alike); stored in 10 W steps | 10000 |
| 13080 | external-EMS / VPP countdown | u16, 1 s | 1–1000; set to 20 when mode 3 or 4 is selected | 0 |

- **Self-consumption (0).** The inverter charges the battery from PV surplus and discharges it to
  cover the house load. The SOC window and the discharge time windows (below) apply.
- **Forced mode (2).** The battery follows 13051 at the power in 13052. "Stop" holds the battery
  idle. The power stage receives the power in 10 W units, never asks the battery for less than
  100 W, and bounds the request by the BMS current limits times the battery voltage and by the
  battery power caps 33047/33048 (at most 10 kW).
- **External EMS (3) and VPP (4)** hand the power decisions to an outside controller.
  - Both run on the countdown at 13080. Selecting mode 3 or 4 sets it to 20; it counts down once
    per second, and at zero the inverter returns to self-consumption (13050 = 0), sets 13051 to
    stop (0xCC) and 13052 to 0. Rewriting the same mode, or writing 13051 or 13052, does not restart
    it: the controller keeps the mode alive by rewriting 13080, which reads back the remaining time.
  - The countdown runs only while an option byte in the same settings block as the
    communication-interruption protection (31453) is 0xAA; that byte is mirrored at input 36527.
- **MicroGrid (8)** is listed by the inverter's own option set.
  - In this mode the DRM function is forced off.
  - A write to the DRM enable is refused.
- **Scheduled forced mode (9)** is accepted at 13050 but carried by no Sungrow document: twelve
  discharge windows and twelve charge windows, each with a power, in two sets selected by a
  day-of-week mask. Inside a window the battery discharges or charges at that power as in forced
  mode; outside the windows the inverter runs self-consumption when one option is on and otherwise
  holds the battery idle. The power stage treats it as forced mode (2).

The order a controller usually writes is power (13052), then mode (13050), then command (13051).
That way the battery never runs a forced command at a stale power.

The "Energy management" page of the dongle's web UI presents the mode at **33146** with the same option
list (0, 2, 3, 4, 8). It does not show 13050–13052. 33146 is the copy of the mode that the
controller pushes to the power stage every 100 ms; the power stage treats mode 4 (VPP) as
external EMS (3) and mode 9 as forced (2).

## Battery SOC window and backup reserve

| Reg | Meaning | Scale | Documented range | Live |
|---|---|---|---|---|
| 13058 | maximum SOC | 0.1 % | 500–1000 (50–100 %) | 1000 |
| 13059 | minimum SOC | 0.1 % | 0–500 (0–50 %) | 0 |
| 13099 | — | — | not served (0xFFFF) | 0xFFFF |
| 13100 | reserved SOC for backup | 1 % | 0–100 | 0 |

The inverter forwards the window to the battery master in CAN frame 0x106:

- min SOC in bytes 0–1;
- max SOC in bytes 2–3.

The inverter applies 5.0 % and 100.0 % as defaults when it first detects a Sungrow battery. The battery keeps its own discharge cut-off
and emergency-charge thresholds as well (19939 and 19941 on the battery slave). See
[battery-interface.md](battery-interface.md).

The inverter raises a low-SOC warning when, among other conditions, the system SOC has stayed at
or below the minimum SOC for 24 hours without a break.

The backup reserve (13100) is the SOC kept back for the EPS port during a grid outage. Normal
self-consumption stops discharging there.

## Export limitation

| Reg | Meaning | Type, scale | Values / range | Live |
|---|---|---|---|---|
| 13074 | export power limit | u16, 1 W | the value ÷ 10 must lie within input 5622–5623 (10 W units); stored in 10 W steps | 10000 |
| 13087 | export (feed-in) limitation switch | u16 enum | 0xAA enable, 0x55 disable | 0xAA |
| 13086 | a related switch; set to 0x55, together with the parallel-link enable, when 33509 is written with 0xA2–0xA4 | u16 | 0xAA / 0x55 | 0xAA |
| 13075 | off-grid (backup) option | u16 enum | 0xAA enable, 0x55 disable | 0xAA |

The "Power regulation" page of the dongle's web UI shows the same function as three settings:

| Reg | Setting | Live |
|---|---|---|
| 31221 | feed-in limitation, 170/85 | 170 |
| 31222 | feed-in limit, 0.01 kW | 10.00 kW |
| 31223 | feed-in limit ratio, 0.1 % | 100 % |

The power-limitation reason (input 5506) never reports the feed-in limit itself (the codes 16 and
64 are not produced by this model): the export limit acts through the active-power limit the
controller pushes to the power stage, which reports as reason 2 and as running state 0x8200
([registers.md](registers.md#running-state-and-status)).

## Battery power caps and capacity

| Reg | Meaning | Scale | Documented range | Live |
|---|---|---|---|---|
| 33047 | battery maximum charge power | 10 W | 1–1500 | 1000 (10 kW) |
| 33048 | battery maximum discharge power | 10 W | 1–1500 | 1000 (10 kW) |
| 33049 | battery capacity override | 0.01 kWh | 0 = use the stacks' reported capacity | 0 |
| 33149, 33150 | battery charging / discharging start power | 10 W | — | not served on P069 |

These caps sit on top of the BMS current limits (input 5635/5636). The BMS limits drop to 0 at
100 % and 0 % SOC. When 33049 is non-zero, input 5639 (battery capacity) reports it instead of
the sum of the stacks' nominal energy.

## Load adjustment and other hybrid controls

| Reg | Meaning | Values | Live |
|---|---|---|---|
| 13002 | load adjustment mode (dry-contact load control); coding as in Sungrow's published hybrid map | 0 timing, 1 on/off, 2 power optimisation, 3 disabled | 3 |
| 13003–13010 | load adjustment time windows and power | — | 0xFFFF (unused) |
| 13011 | load on/off (on/off mode) | 0xAA on, 0x55 off | 0x55 |
| 13017 | forced start-up under low SOC | — | — |
| 13089, 13090 | active power limitation in Sungrow's published hybrid map | not implemented on P069: read 0xFFFF, accept only 0xFFFF | 0xFFFF |
| 30043 | factory reset | — | do not write |
| 30230 | global MPPT scan | 170 starts | an I-V curve scan of the PV inputs, one input at a time (about 25 s each). The controller can also start it by itself: with two option bytes at 170 it writes 30230 = 170 every n minutes, and a further option starts a scan at a set time of day; the registers behind these options are not identified |

The dongle's web UI also has these energy-management and operation settings:

| Page | Reg | Setting | Live |
|---|---|---|---|
| Energy management | 33151–33158 | weekday discharge windows 1 and 2 (start/end hour and minute) | 00:00–00:00, 00:00–24:00 |
| Energy management | 33179 | battery use on non-working days (170/85) | 85 |
| Energy management | 33208 | forced charging (170/85) | 85 |
| Energy management | 33275 | digital output: 0 off, 1 load control, 2 grounding-fault indication, 3 microgrid | 0 |
| Energy management | 32617 / 32618 | off-grid emergency charging (170/85), target SOC 1–100 % | 170, 10 % |
| Energy management | 33276–33280 | generator: start/stop control (170 forced start, 85 forced stop, 165 by SOC, 166 manual), max AC charging power (0.01 kW), generator rating (0.1 kW), start / stop SOC (0.1 %) | 85, 30.00 kW, 5.0 kW, 20.0 %, 90.0 % |
| Operation | 32998 | power limit for energy purchase (0.01 kW) | 13.00 kW |
| Operation | 32708 | PV connection: 161 independent, 162 parallel, 163 partial parallel | 161 |
| Operation | 32307 | minimum output voltage when off-grid (0.1 %) | 70.0 % |
| Power regulation | 31204 / 31205 | active power limitation (170/85) and ratio (0.1 %) | 170, 100.0 % |
| Power regulation | 31200–31202 | active power ramp control (170/85), down and up rates (%/min) | 170, 16, 16 |
| Power regulation | 31216 | whole-home backup mode (170/85) | 85 |
| Power regulation | 31227 / 31228 | metering phase (10/11/12 = meter phase A/B/C), meter CT ratio | 10, 1 |

The meaning of 170 and 85 throughout: 170 (0xAA) = enable, 85 (0x55) = disable/off.

## What the dongle and the cloud expose

**Dongle web UI.** The inverter's parameter pages are Initial, Operation, Protection, General,
Power regulation, Protection (other) and Energy management. They read and write the 3xxxx
registers listed above and on [grid-codes-and-drm.md](grid-codes-and-drm.md), plus 5006 on the
General page. The 13050–13100 controls and 33047/33048 appear on **no** web-UI page, although all
of them read back normally.

**Dongle Modbus TCP (port 502).**

- The dongle forwards any holding-register read or write at unit 1 to the inverter. It keeps the
  client's function code (5, 6 or 16).
- It applies no register list and no range check of its own.
- The success reply is rebuilt from the request.
- Any failure becomes **exception 04**, after the dongle's 5 s context timeout or sooner. The
  inverter's own exception code, for example 05, is never passed to the TCP client.
- Unknown function codes also end in exception 04 after about 9 s.

See [`../../protocols/modbus-tcp.md`](../../protocols/modbus-tcp.md) and
[`../../protocols/writes-and-meter-sync.md`](../../protocols/writes-and-meter-sync.md).

**iSolarCloud.** The app's parameter pages write through the cloud's parameter-set command. The
dongle turns each request into a function-16 write to the inverter.
[`../../protocols/cloud-commands.md`](../../protocols/cloud-commands.md) describes the command and
its point addressing.

**Polling.** The dongle does not poll the controls on this page (5006, 13000–13100,
33047–33049) cyclically. It reads them on demand only:

- when a web-UI parameter page opens;
- when a Modbus-TCP client asks (pass-through);
- when the cloud requests a parameter read.

The dongle's cyclic poll covers the input registers on [registers.md](registers.md).

## Not yet known

- Which setting enables the 13080 countdown, and which registers hold the mode-9 schedule and the automatic I-V scan options.
- What 13086 controls.
- The controller's rule set for 30000–36000 (ranges and relations), beyond the copy the web UI enforces; the power stage's own fixed ranges are a second, silent filter.
