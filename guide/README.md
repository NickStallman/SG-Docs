# Guide

The readable stream: what is going on inside a Sungrow residential system, chapter by chapter.
Read in order, or jump to the chapter you need. Each chapter links to the
[reference](../reference/) for the exact numbers.

1. [The big picture](01-big-picture.md): how inverter, dongle, battery, cloud and local network fit together.
2. [Inside the dongle](02-inside-the-dongle.md): what the WiNet-S and WiNet-S2 are, how they differ, LEDs, button, first-time setup.
3. [Talking to the inverter](03-talking-to-the-inverter.md): RS-485 polling, device discovery, and the change-stream trick that makes 9600 baud enough.
4. [Talking to the cloud](04-talking-to-the-cloud.md): the MQTT link to iSolarCloud, what leaves the house and how often, how commands come back.
5. [Local access](05-local-access.md): the web interface, Modbus TCP, mDNS, and what Home Assistant integrations actually use.
6. [Firmware updates](06-firmware-updates.md): how the dongle updates itself and how it pushes firmware into the inverter and battery.
7. [Inside the inverters](07-inside-the-inverters.md): the SH10RS and SG5.0RS from the communication side.
8. [Inside the battery](08-inside-the-battery.md): the SBR192's control unit, cell monitoring, state of charge and the balancing process.
9. [Security and privacy](09-security-and-privacy.md): what is exposed on the LAN and to the cloud.
10. [Notable findings](10-notable-findings.md): the surprising parts, collected.
