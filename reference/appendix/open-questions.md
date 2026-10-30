# Open questions

What is still not known about these devices, grouped by device. Each item is either a question or
a plain statement of what is missing. Register addresses are 1-based, as in Sungrow's published
Modbus documentation.

## Both dongles

- The part number of the RS-485 transceiver on the WiNet-S and the WiNet-S2 is not identified.
- Are the LEDs active-low on both boards?
- Does the RS-485 transceiver echo transmitted bytes back to the receiver?
- What condition makes the dongle show its slow blue LED blink is not identified.
- How quickly does the dongle retry an inverter it has marked offline?
- How the dongle derives about a hundred of its reported values (totals, combined powers, voltages) from the raw registers is not fully known.
- How the dongle computes the live allowed range of about 200 installer settings from the inverter's rated voltage, grid frequency and model is not fully known.
- Does the factory-default identity on production units include the dongle's cloud identity, so that a factory reset keeps it?
- Which identity values does provisioning change from their defaults on a real unit?
- What does the Modbus TCP server return for a missing float32 meter value?
- What does the Modbus TCP server reply when a live read cannot be sent because the RS-485 bus is busy?

## WiNet-S

- The location of the UART0 pads (GPIO1 TX, GPIO3 RX) on the WiNet-S board is not recorded.
- Is the RS-485 driver-enable line on GPIO12 (also the flash-voltage strapping pin) pulled up or down at boot? A pull-up would select 1.8 V flash.
- Does the button input on GPIO36 (input-only, no internal pull) have an external pull-up?

## WiNet-S2

- The supervisor IC on the WiNet-S2, its timeout and the polarity of its GPIO2 enable line are not identified.
- Why does the WiNet-S2 switch the supervisor off in its brownout handler: would the supervisor otherwise reset the board during a brownout?
- Is the W5500 Ethernet controller's reset line wired to GPIO5? The firmware pulses GPIO5 when it starts the Ethernet driver.
- Is UART0 reachable on the WiNet-S2 board, and where?

## SH10RS

- The SH10RS firmware handles Modbus function code 0x20; which device sends it is unknown.
- What does the multi-unit link configured at register 36110 do?
- The controller's rules for validating writes to holding registers 30000–36000 are not fully known (the power stage's own fixed ranges are).
- Which conditions raise inverter fault codes 210–211 and 303–304? The other first-level codes are listed in [error-codes.md](error-codes.md#inverter-fault-codes-sh10rs).
- Does an immediate read-back after a write to a holding register in 30000–36000 return the new value? (Writes to the 13000–13370 window do read back at once.)
- Which holding registers hold the automatic I-V scan options, the schedule of work mode 9 (scheduled forced mode) and the switch that enables the external-EMS countdown at 13080?

## SG5.0RS

- What exactly each SG5.0RS register window holds is not fully mapped.

## SBR battery

- The meaning of the bits in the battery capability word at register 10699 is unknown.
- The meaning of several battery protection ids is unknown.
- The peer on the far end of the CAN 0x5F0 tunnel is not identified.
- What is wired to the battery control unit's USART3 status port is not identified.
- Is 13119 on protocol-type-5 inverters in 0.1 kWh, like 5637, or in Wh? Sungrow's app table says 0.1 kWh, the dongle's web UI catalogue says Wh; no live read is available.

## Other devices

- In the DTSD1352 meter table (type 6100) two values share register 4161, so the following energy rows appear shifted by one; the correct addresses are not confirmed on a real meter.
- Which of the meter registers 4125–4129 and 4133–4137 are per-phase reactive power and which are apparent power, and what scale do they use?
- What do meter registers 4153 and 4154 hold?
- What does Energy Bridge register 4162 hold? Four other Energy Bridge values have no name either.
- On protocol-type 1/4 inverters, is the total yield at 5004 in 1 kWh or 0.1 kWh?
- On protocol-type 1/4 hybrids, which of the protection registers 30500, 30643 and 30646 exist?
- Whether a protocol-type 4 inverter, a protocol-type 5 hybrid and a protocol-type 2 hybrid honour the start/stop registers the dongle addresses on them (30045, 13000 and 5006, 0xCF start / 0xCE stop).
- For larger inverters, the names of MPPT inputs 13–20 and strings 25–40 are not confirmed by any Sungrow document.
- On protocol-type 5 hybrids, are Modbus TCP registers 13001, 13010, 13017, 13036, 13045 and 5214 populated? The dongle fills them from different poll values than on other protocol types.
- Is the forced charge/discharge power in watts on every hybrid model? Some Sungrow documents give it in percent.
- The modulation the MPLC power-line link uses on the DC string cable to reach optimizers is not identified.

## Cloud

- Is 16668 the production broker port, or does the cloud push a different one with its server-switch command?
- Which certificate authority does the broker's certificate chain to?
- Does the dongle check certificate validity dates?
- Are cloud commands published as retained messages?
- How does the cloud reassemble a chunked rundata message: in packet-id order?
- The exact timing of the server-switch confirm and rollback timers is not confirmed.
- What range of rundata upload periods and quiet hours does the dongle accept?
- What do several of the dongle's own logger-level values mean?
- How does the dongle format meter float values in uploads?
- Are string and hex values shorter than their field size zero-padded in uploads?
- Is the dongle itself included in the delayed property-data message?

## Firmware update

- Does the iSolarCloud update service decide offers from the firmware version string the dongle reports?
- Does the dongle accept an update package that carries only the application image and leave the web-interface partition untouched?
- Does the dongle grow the 3 MB web-interface image to fill its partition?
- Whether every production batch ships with Secure Boot and flash encryption disabled; the WiNet-S and WiNet-S2 examined both have them off.
