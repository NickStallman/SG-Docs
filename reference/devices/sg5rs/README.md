# SG-RS string inverters (SG5.0RS)

The SG-RS series is Sungrow's single-phase residential string inverter family. The member described here is the
**SG5.0RS**: 5 kW rated active power, 3 kvar rated reactive power, 5 kVA maximum apparent power, single-phase output,
two MPPT inputs, no battery port. It is the inverter of a string-inverter site (SG5.0RS) whose dongle is a WiNet-S.

The SG5.0RS uses the same controller design as the SH10RS hybrid: an STM32F417 controller (the "ARM") that owns the
communication ports and serves the Modbus register map, a power-stage DSP behind it, and an arc-fault (AFCI) board.
Most of what the [SH10RS pages](../sh10rs/) say applies here unchanged. These pages describe only what is different.

## Identity

| Item | Value | Where it is read |
|---|---|---|
| Device code | `0x2606` (9734) | input register 2515 |
| Model string | `SG5.0RS` | input registers 2516–2523, and 34209 (holding) |
| Protocol code | `AA10` | input registers 2534–2535 (four ASCII characters) |
| Protocol tag | `ZS` | input register 2578 |
| Dongle device class | 21, residential string inverter (device code range `0x2400`–`0x27FF`) | derived by the dongle |
| Protocol type | 3, the incremental (function `0x65`) protocol selected by the `ZS` tag | derived by the WiNet-S2; the WiNet-S publishes 2 for the same unit, with the same outcome |
| Settings-template family | `unit` | from (inverter type, protocol type); see [device types and grid profiles](../device-types-and-grid-profiles.md) |
| Inverter type (holding 30644) | `0xFFFF` (not served) | holding register 30644 |
| Country (holding 32310) | 6, Australia | on a string-inverter site |
| Grid standard (holding 30036) | 13, AS/NZS 4777.2:2020 Australia A | on a string-inverter site |
| Grid code (holding 32311) | 0 | on a string-inverter site |
| Grid-profile variant input (5479) | 1, PV variant | input register 5479 |
| Firmware package | `SUNSTONE-S_B000.V001.P051` | iSolarCloud package; controller image `SUNSTONE-S_01011.01.50` |
| Installed controller build | `SUNSTONE-S_01011.01.49` | input register 2582 on a string-inverter site |
| ARM / DSP software identifiers | `ARM_SUNSTONE-S_V11_V01_A` / `MDSP_SUNSTONE-S_V11_V01_A` | input registers 4954 / 4969 |

Register numbers on these pages are the 1-based numbers of Sungrow's published Modbus documentation; the address in
the Modbus frame is one less.

Which inputs decide the dongle's view of the inverter: only the device code, the protocol code at 2534–2535, the tag
at 2578 and holding register 30644. The model string plays no part.

## Pages

| Page | Contents |
|---|---|
| [architecture.md](architecture.md) | Ports, register windows, the slave-address gate and relay setting, the encrypted Bluetooth link, the optimizer (MLPE) link, DRM handling, firmware images |
| [registers.md](registers.md) | Registers that differ from the SH10RS: yields and operating time, reactive-power mode, power-limit setpoint, start/stop, the PV grid-profile variant |

Everything not listed on these two pages behaves as described for the SH10RS:

| Topic | SH10RS page |
|---|---|
| Controller, DSP and AFCI design | [../sh10rs/architecture.md](../sh10rs/architecture.md) |
| Window dispatch rules, DSP forwarding | [../sh10rs/register-windows.md](../sh10rs/register-windows.md) |
| Common register set | [../sh10rs/registers.md](../sh10rs/registers.md) |
| Grid codes, protection settings, DRM | [../sh10rs/grid-codes-and-drm.md](../sh10rs/grid-codes-and-drm.md) |
| Firmware update through the dongle | [../sh10rs/firmware-update.md](../sh10rs/firmware-update.md) |
| Control (start/stop, power limits) | [../sh10rs/control-and-ems.md](../sh10rs/control-and-ems.md) |

Related pages elsewhere: [meters](../meters.md) (the SG5.0RS has its own meter port), [optimizers and
MLPE](../optimizers-and-mlpe.md) (the MLPE-CSU firmware ships in the SG5.0RS package), and the generated register
table [../../appendix/register-tables/rs485-inverters.md](../../appendix/register-tables/rs485-inverters.md).
