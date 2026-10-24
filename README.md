# Sungrow residential equipment: how it works

Independent documentation of the communication side of a Sungrow residential solar system:
the **WiNet-S** and **WiNet-S2** communication dongles, the **SH10RS** hybrid inverter, the
**SG5.0RS** string inverter, the **SBR192** battery, and the other device types the dongles know
about. It describes what the devices do: the hardware inside the dongles, the buses and protocols
between inverter, battery, dongle, local network and the iSolarCloud service, the firmware-update
containers, and the register maps.

The material comes from studying the devices on the bench, their traffic, and their published
firmware files. It describes observable behaviour and formats. No credentials, keys, certificates
or serial numbers are published here.

## Two ways to read this

| Stream | For whom | Start at |
|---|---|---|
| **Guide** | owners, installers, Home Assistant users who want to understand what is going on inside | [guide/](guide/) |
| **Reference** | developers and integrators who need pinouts, byte layouts, register addresses and protocol details | [reference/](reference/) |

The guide links into the reference wherever a chapter touches a detail. The reference stands on
its own.

## Devices covered

| Device | Role | Firmware described | Hardware on the bench |
|---|---|---|---|
| WiNet-S | Wi-Fi/Ethernet dongle, ESP32 | V200 P046 (pull-update client: P044) | yes |
| WiNet-S2 | Wi-Fi/Ethernet dongle, ESP32-S3 | SV300 P032 | yes |
| SH10RS | single-phase hybrid inverter (PV + battery + backup) | package P069 | yes |
| SG5.0RS | single-phase string inverter | package P051 | yes |
| SBR192 | high-voltage LFP battery, 6 modules, 19.2 kWh | control unit 22011.01.30 | yes (two stacks) |
| Meters, EV charger, Energy Bridge, SP600 optimizers, WiMeter / WiProxy | accessories | as handled by the dongle firmware | no |

## Guide

1. [The big picture](guide/01-big-picture.md): inverter, dongle, battery, cloud, local network.
2. [Inside the dongle](guide/02-inside-the-dongle.md): what a WiNet does, S versus S2, LEDs, button, setup.
3. [Talking to the inverter](guide/03-talking-to-the-inverter.md): polling, discovery, the change stream.
4. [Talking to the cloud](guide/04-talking-to-the-cloud.md): what is sent, how often, how commands arrive.
5. [Local access](guide/05-local-access.md): web UI, Modbus TCP, mDNS, what integrations rely on.
6. [Firmware updates](guide/06-firmware-updates.md): the three update channels and the signed containers.
7. [Inside the inverters](guide/07-inside-the-inverters.md): controller and DSP, register windows, DRM, grid codes.
8. [Inside the battery](guide/08-inside-the-battery.md): cell monitoring, state of charge, the balancing process.
9. [Security and privacy](guide/09-security-and-privacy.md): what is exposed, where and to whom.
10. [Notable findings](guide/10-notable-findings.md): the surprising parts, in one place.

## Reference

- [Hardware](reference/hardware/): WiNet-S, WiNet-S2, S versus S2, flash layout, boot and runtime, LEDs and button.
- [Protocols](reference/protocols/): RS-485 master, discovery, function codes, Modbus TCP, web API, local services, setup, remote access, cloud MQTT, envelope, messages, commands, diagnostics, third-party upload.
- [Firmware update](reference/firmware-update/): the update container, the three channels, the relay to inverter and battery.
- [Devices](reference/devices/): [SH10RS](reference/devices/sh10rs/), [SG5.0RS](reference/devices/sg5rs/), [SBR192](reference/devices/sbr192/), meters, EV charger and Energy Bridge, optimizers, WiMeter and WiProxy, device types and grid profiles.
- [Appendix](reference/appendix/): generated register tables, error codes, open questions.
- [Glossary](GLOSSARY.md).

## Related

**Rewired-S**, an open replacement firmware for the WiNet dongles built from this documentation,
is coming soon at <https://github.com/NickStallman/rewired-s>.

## Licence

Text and diagrams are licensed under [Creative Commons Attribution 4.0 International](LICENSE)
(CC BY 4.0). Sungrow, iSolarCloud, WiNet, SH10RS, SG5.0RS and SBR are trademarks or product
names of Sungrow Power Supply Co., Ltd. This project is not affiliated with or endorsed by Sungrow.
