# Reference

The technical stream. Pages are self-contained; tables carry the exact values.

Conventions used throughout:

- Modbus register addresses are **1-based**, as in Sungrow's published Modbus documentation.
  The value on the wire is the address minus one.
- 32-bit register values are **low word first** unless a page says otherwise.
- Function codes and CAN identifiers are hexadecimal (`0x65`, `0x1E0`); register addresses are decimal.
- "The dongle" means either WiNet; where the WiNet-S and WiNet-S2 differ, the page says which.

## Sections

- [Hardware](hardware/): what is on the WiNet-S and WiNet-S2 boards, pinouts, flash layout, boot sequence, LEDs and button.
- [Protocols](protocols/): every bus and network protocol the dongle speaks, from RS-485 to the cloud.
- [Firmware update](firmware-update/): the update container format, the update channels, and the relay of firmware into inverters and batteries.
- [Devices](devices/): the inverters, the battery and the accessory device types.
- [Appendix](appendix/): generated register tables, error codes, open questions.
