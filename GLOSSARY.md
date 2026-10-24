# Glossary

**.sgu**: the dongle's own firmware-update container (header, typed body parts, optional signature trailer).

**.sgz / IAP 3.0**: a combined container holding several module images with per-module version gates.

**1-based address**: register number as printed in Sungrow's documentation, one more than the wire address.

**1-based register number**: the register number used in Sungrow documents, one more than the PDU address.

**10308**: the cloud command that pushes a firmware job (server, device list, files) to the dongle.

**10376**: the cloud reply listing firmware package URLs for the dongle's connected devices.

**access layer**: the dongle's session/parameter-checking code that gates every local-API route.

**action frames**: 802.11 vendor management frames the WiNet-S2 uses to pass Wi-Fi credentials dongle-to-dongle.

**address claim**: a 0x600 + r frame an unassigned BCU sends during an election.

**AES-128-ECB**: block cipher mode without chaining, used to wrap the app's Modbus frames.

**AFCI**: arc-fault circuit interrupter board, CAN node 6.

**alert file**: XML file describing one fault or alarm event turning on or off.

**allow-list**: the up-to-five exact source IPs allowed to connect to the Modbus-TCP listeners.

**app rollback**: the bootloader returning to the previous slot when a new one is not marked valid.

**application slot**: one of two flash partitions holding dongle firmware, one running and one for the next update.

**ARM**: the inverter's STM32 communication and control processor.

**AS/NZS 4777.2**: Australian/New Zealand standard for grid-connected inverters, 2015 and 2020 editions, regions A/B/C in 2020.

**auth-code verification**: the app's cloud-checked commissioning step that raises a local session to admin.

**back-fill**: re-reading an older time window from the dongle's history after a failed upload.

**back-off window**: per-device random reply delay set through register 4886.

**backup (EPS) port**: output that keeps supplying loads during a grid outage.

**backup reserve**: SOC kept for the backup port during an outage.

**balancing**: bleeding charge from high cells through resistors so all cells end up level.

**batchId**: the cloud's id for one command, echoed in the reply and used for de-duplication.

**battery slaves**: Modbus addresses 200–204 that the inverter answers on behalf of the battery stacks.

**BCU**: battery control unit, the management computer on top of an SBR stack.

**BCU tunnel**: Modbus RTU carried over battery CAN to the battery control unit.

**beat**: the online flag in beatdata.

**BMS**: battery management system, here the SBR battery master.

**broker**: the MQTT server the dongle connects to (iot.isolarcloud.com:16668 by default).

**businessData**: the payload array of device elements or command fields.

**CAN bus**: a two-wire bus used between the inverter and battery.

**CAN1**: the BCU's CAN bus to the inverter and the other stacks.

**CAN2**: the BCU's 29-bit service and stack-to-stack bus.

**capability bitmap**: 60-register block at 5520 in which a protocol-2/3 inverter reports which optional registers it has.

**capability block**: 60 input registers at 5520 in which the inverter announces its optional feature groups.

**capability word**: a register whose bits announce which optional features a device has.

**capacity mode**: balancing on estimated surplus charge after a low-SOC calibration.

**capacity-mode balancing**: bleeding cells by their estimated surplus charge after an SOC calibration.

**cell monitor**: a chip that measures a group of series cells and switches their balancing resistors.

**cell-sampling fault**: the BCU's summary level for untrustworthy cell data.

**change stream**: Sungrow function 0x65, in which the inverter reports only registers that moved beyond a deadband.

**child device**: a device the dongle adds because the inverter reports it, not because it was searched for.

**chunk**: one MQTT message carrying up to 2560 bytes of a longer businessData.

**cloudId**: the region id the dongle fetches from auto.isolarcloud.com.

**cmdType**: numeric string in a cloud command that selects the dongle's handler.

**command counter**: a 6-bit value that second-generation monitors return with each read so lost commands can be noticed.

**command slot**: the dongle's single in-flight cloud request, which blocks further requests until answered or expired.

**communication controller**: the ARM microcontroller in a Sungrow inverter that owns all external ports and answers Modbus.

**communication-loss fail-safe**: SG5.0RS feature (30105/30106) that clamps export when Modbus traffic stops.

**CONNACK**: the broker's answer to an MQTT CONNECT.

**contactor**: the heavy-duty switch that connects a battery stack to the inverter.

**continue**: 0x65 request with sub-code 3 that fetches changes since the last reply.

**core dump**: a snapshot of the crashed firmware's state written by the panic handler.

**coulomb counting**: estimating charge by adding up current over time.

**CRC-32/MPEG-2**: the non-reflected CRC-32 variant computed by the STM32 hardware CRC unit.

**dataType**: string code in parameter commands giving the value's width and signedness.

**DE**: driver-enable line that switches an RS-485 transceiver to transmit.

**deadband**: the minimum change, in raw register units, before the inverter reports a register again.

**debug-log stream**: the WiNet-S feature that streams the console log to an arbitrary TCP host, armed by one Modbus write.

**deep-discharge latch**: a one-hour timer that stops balancing and monitor polling after a deep discharge until restart.

**derating table**: a per-module table of allowed charge current by temperature and SOC.

**detached signature**: the separate .bin file holding the RSA-2048 signature over the whole .sgu.

**devCode**: the dongle's own number for a device (1, 2, … and 247 for itself), not its Modbus address.

**develop login**: the vendor factory web login (level 4), unlockable only while the rdswitch window is open.

**develop role**: the vendor factory login, gated by a rotating password and the remote-maintenance window.

**device code**: model number a Sungrow device reports, for example 0x0D1B for the SH10RS.

**device code 247**: the identifier that marks the dongle itself as the target of an update job.

**device type**: the dongle's class for a device (1 string, 21 residential, 35 hybrid, 18 meter, 44 battery, ...).

**device type 18**: the dongle's class for an energy meter ("ammeter").

**device type 34**: the dongle's class for Sungrow logger-family accessories.

**device type 46**: the dongle's class for an EV charger.

**device type 92**: the dongle's class for an Energy Bridge.

**devType**: the cloud's device-type number (14 hybrid, 1 string inverter, 43 battery, 22 logger, 7 meter).

**dispatch list**: 0x60 mode in which one broadcast frame carries power commands for several inverters.

**dispatching mode**: a stored Japan output-control setting that only changes what the web time page offers.

**DL/T 645**: a Chinese serial electricity-meter protocol; parsed but not usable on these dongles.

**DRED**: demand response enabling device, the network operator's box on the inverter's DRM port.

**DRM**: AS/NZS 4777.2 demand response mode, a DRED command that limits generation or consumption.

**DRM0–DRM8**: the AS/NZS 4777 demand-response modes (disconnect, consumption limits, generation limits).

**DSP**: digital signal processor that runs the inverter's power electronics.

**DSP forward**: a request the inverter relays to its power-stage processor, pausing its other ports.

**DSP forwarding**: the controller passing a Modbus request on to the power-stage DSP and relaying the reply.

**DSP-forward hold**: the time an inverter stops serving its other ports after relaying a request to its DSP.

**DSP-forward window**: a register range whose requests the controller relays to the DSP, holding its other ports for at least 1.5 s (SH10RS) or 10 s (SG5.0RS).

**eFuse**: one-time-programmable ESP32 bits holding security settings such as Secure Boot.

**element**: one device's entry in businessData.

**EMAC**: the ESP32's built-in Ethernet MAC.

**EMS mode**: the inverter's energy-management strategy (self-consumption, forced, external EMS, VPP, MicroGrid).

**Energy Bridge**: Sungrow accessory on a hybrid inverter's meter port, address 246, that supplies grid measurements.

**enforcement switch**: holding register 36332 that decides whether a failed signature blocks the update.

**enum suffix**: the enum_<encrypt>_<keynum>_<compress> tail of every cloud topic.

**envelope**: the JSON wrapper (messageId, packgeCount, packgeId, publicData, businessData) around every cloud message.

**EPS**: emergency power supply, the backup output.

**ESP32**: Espressif's Wi-Fi microcontroller used in the WiNet-S.

**ESP32-S3**: Espressif's dual-core Wi-Fi/BLE microcontroller used in the WiNet-S2.

**EV charger**: Sungrow AC charging station, address 248, reached through the hybrid inverter.

**exceptCode**: per-module exception bytes that accompany a failed cloud device update.

**exception 02**: Modbus "illegal data address" reply.

**exception 04 / 05**: Modbus error replies for a refused value and for a register that does not apply to the device.

**exception 4**: Modbus "server device failure" reply.

**export limitation**: cap on power fed to the grid, measured at the meter.

**factory reset**: wiping the dongle's settings back to the factory copy.

**FCAS**: Australian Frequency Control Ancillary Services market, for which the dongle records fast data around frequency events.

**feed-in limit schedule**: cloud-supplied time-of-day export limit that the dongle applies to 31221/31222.

**file code**: number written to register 32000 that selects which inverter file to export.

**forced mode**: EMS mode in which the battery follows an explicit charge/discharge command.

**FRAM**: ferroelectric non-volatile memory that holds the BCU parameters.

**frame gap**: idle time the dongle waits before sending each RS-485 request (18 ms for inverters).

**frame-log capture**: timed recording of raw bus frames into a text file on the dongle.

**full call**: 0x65 request with sub-code 2 that makes the inverter send every tracked register.

**function word**: inverter capability bits in input registers 5487–5488 that the dongle reads to choose register sets and features.

**functionEnum**: the 16-byte capability bitmap in propertydata that gates cloud features such as Live data.

**Fupin**: 扶贫 "poverty alleviation", a Chinese national PV monitoring platform the dongle's dormant Fupin link would report to.

**grid code**: the country or region profile that sets protection limits and which features an inverter offers.

**grid profile**: the set of protection registers written for one grid standard.

**hand-over**: the master stack passing a received image to the other stacks over CAN2.

**heartbeat**: the 1 Hz 0x500 + 0x20n frame carrying address, master flag, commands and system SOC.

**holding register**: read/write Modbus register (function codes 3, 6, 16).

**HOME read**: broadcast function-3 read of register 30035 that asks a device for its slave address.

**host-name verification**: checking that a TLS certificate was issued for the server being contacted.

**hotspot**: the dongle's own open access point SG-<serial> at 11.11.11.1/24.

**HT20**: a 20 MHz-wide Wi-Fi channel.

**hybrid inverter**: an inverter that also charges and discharges a battery.

**I-V curve scan**: inverter-driven sweep of a PV input that records current against voltage.

**IAP**: in-application programming — updating a device's firmware through its running application rather than a separate flasher.

**IAP mailbox**: the block of Modbus registers through which an update image is written packet by packet.

**IAP stage**: the step the inverter's bootloader mailbox reports (idle, open, failed, success, sub-module phase).

**identity file**: the record file on the external flash holding the dongle's serial number and configuration.

**IEC 60870-5-104**: a SCADA telemetry protocol over TCP; offered by the dongle but never actually served.

**indirect read**: a register whose content is chosen by a selector written beforehand.

**inverter mode 3**: the BCU state, set by the inverter's handshake, in which it sends its full telemetry every second.

**inverter type**: holding register 30644, used with the protocol type to pick a settings template.

**IO-MUX default**: the pin a peripheral uses when the firmware does not route it elsewhere.

**iSolarCloud**: Sungrow's monitoring cloud and phone-app back end.

**isoSPI**: Analog Devices' transformer-isolated two-wire SPI link used to daisy-chain cell monitors.

**J1939**: the SAE vehicle-bus convention of 29-bit identifiers made of priority, PGN and source address.

**Japan time enable**: a persistent flag that, when set, blocks cloud/Modbus clock writes and boot time-zone/NTP setup.

**join-charge request**: the master asking the inverter to charge so a higher-voltage stack can connect.

**kick**: a level change on the watchdog input that restarts its timeout.

**LAN pull**: the dongle fetches its own firmware as an HTTPS client from a server on the local network, driven by a browser.

**last will**: a message the broker publishes on the dongle's behalf when its session drops.

**learned capacity**: a module's nominal capacity multiplied by its state of health.

**LFP**: lithium iron phosphate cell chemistry, 3.2 V nominal.

**link name**: identifier of one outbound or server link on the transmit pages.

**link voltage**: the voltage on the inverter side of the stack contactors.

**LittleFS**: the on-flash filesystem holding the web UI, written from a type-3 container part.

**live read**: an FC 3 request the dongle forwards to the device instead of answering from the cache.

**lock-out**: a non-volatile fault flag that keeps a stack switched off across power cycles.

**logexport**: cloud command 10329, which uploads collected logs to a command-supplied SFTP server.

**logger**: the dongle as a device in its own uploads.

**logger point**: a dongle-local setting addressed by a point id in command 10384, with devCode 247.

**low word first**: 32-bit values whose first register holds the least significant 16 bits.

**lwIP**: the lightweight TCP/IP stack shipped with ESP-IDF.

**machine type**: an inverter input register (5479) identifying the hardware variant.

**mailbox**: a reserved Modbus register range the device firmware treats as a bootloader channel, not as data.

**master**: the device that sends requests on a Modbus bus (the dongle).

**master BCU**: the BCU elected to speak for a parallel group and send the system frames.

**master stack**: the stack whose BCU holds the master role, sends the system frames and sequences contactor closing.

**MBAP**: the 7-byte Modbus-TCP header (transaction id, protocol id, length, unit id).

**mbedTLS**: the TLS library shipped with ESP-IDF.

**mc_cloud**: link name of the third-party FTP/SFTP XML upload.

**mDNS**: multicast DNS, the LAN service-discovery protocol used over UDP 5353.

**MDSP / SDSP**: master and slave DSP of the power stage.

**meter port**: the inverter's own RS-485 port on which it polls the grid meter as Modbus master.

**meter sync**: the dongle writing its own meter's readings into the inverter, which then ignores its meter port.

**MLPE**: module-level power electronics (optimizers and rapid-shutdown devices).

**MLPE-CSU**: the controller of the optimizer network inside the SG5.0RS.

**Modbus exception**: the standard error reply (e.g. 0x04 slave device failure) a device returns for a bad request.

**Modbus register**: a numbered 16-bit value slot in a device's Modbus map.

**Modbus RTU**: the binary request/response protocol used on the RS-485 bus.

**Modbus TCP**: the Modbus register protocol carried over a TCP network connection.

**Modbus TCP map**: the registers the dongle serves to LAN clients on port 502 for one device type.

**Modbus-over-CAN tunnel**: a Modbus RTU frame carried as consecutive 8-byte CAN frames on one identifier.

**Modbus-TCP client link**: a reverse-connected link where the dongle dials out and the remote host polls it as master.

**module**: a 64 V block of 20 series LFP cells with its own cell monitors and EEPROM.

**module code**: the two digits after the first underscore of an image's module string, which pick the update route.

**module string**: the 30-byte name/version a device image carries (e.g. SUNSTONE-H_01011.02.69), used to match the image to a module.

**module-limited limit**: the lowest current limit over the modules of a stack.

**monitor loop**: the dongle's 3 s housekeeping loop in its main task.

**MPLC**: power-line communication over the DC string cable to module-level devices.

**MQTT**: a lightweight publish/subscribe messaging protocol used between the dongle and iSolarCloud.

**multiplexed frame**: one CAN identifier whose first bytes select which values follow.

**MUTE/UNMUTE**: monitor commands that suspend and resume cell discharge.

**near mode**: the iSolarCloud app talking to the dongle directly over the LAN or hotspot instead of through the cloud.

**network-config mode**: dongle mode entered by a 5 s button hold to pair a WiProxy.

**notice**: unsolicited WebSocket frame announcing a session eviction or timeout.

**NVS**: ESP-IDF's key/value store in internal flash.

**octal PSRAM**: external RAM on an 8-bit-wide bus.

**OCV**: open-circuit voltage, the rest voltage of a cell that maps to its state of charge.

**open-wire test**: a measurement with pull-up and pull-down currents that reveals a broken cell sense wire.

**optimizer module**: the inverter's internal link to SP600-class module-level electronics, reached with codes 0x70 to 0x7A.

**OTA slot**: one of the two application partitions; an update is written to the inactive one and run after reboot.

**otadata**: the small partition recording which OTA slot boots.

**parameter operation**: an on-demand read or write job that pauses polling of that device until it ends.

**partition table**: the list of named regions of the internal flash, at offset 0x8000.

**pass-through relay**: the /device/passthroughway route that sends a raw RTU frame to the RS-485 bus.

**passive balancing**: equalising cells by bleeding charge from the higher ones through resistors.

**PEC**: packet error code, the CRC that protects each isoSPI command and register transfer.

**per-stack frame**: a frame in 0x700 + 0x20n + k carrying one stack's own data.

**PHY**: the chip that drives the Ethernet cable.

**PKCS#1 v1.5**: the RSA signature padding scheme used here, applied with no DigestInfo wrapping.

**placeholder value**: a fixed value an inverter serves in place of a real reading.

**point id**: iSolarCloud's number for one value of one device class.

**point table**: list of register rows (address, count, type, scale) describing a device's map.

**poll cache**: the dongle's in-memory copy of the last values it read from each device.

**poll class**: per-row poll period in 100 ms units in the dongle's poll tables.

**poll map**: the list of registers the dongle reads cyclically from a device over RS-485.

**port arbiter**: the controller's lock that lets only one slave port be served during a forward.

**power switch**: the inverter start/stop register (5006, 13000 or 30045 depending on family).

**power-flow flags**: bitfield at 6099/13001 saying which way energy flows.

**power-stage DSP**: the inverter's signal processor that runs the power electronics and is reached only through the controller.

**PPP**: point-to-point protocol, used over cellular links; linked into the WiNet-S2 image but never used.

**pre-charge**: limiting the inrush current through a resistor before the main contactor closes.

**priority queue**: the port queue for on-demand frames, served before cyclic polling.

**private 5xxx map**: the Sungrow register layout that dongles poll for protocol type 3 inverters.

**private function code**: Sungrow Modbus function code in 0x70–0x7E.

**private function codes**: Sungrow-specific Modbus function codes 0x70–0x7E used for optimizer traffic.

**private map**: Sungrow's 5000-series input-register layout that the dongle polls for protocol-type-3 inverters.

**product type**: the numeric model code a dongle reports to iSolarCloud (0x715 WiNet-S, 0x720 WiNet-S2).

**product_func_enum1**: per-build feature bit word the phone app reads from /product/list.

**protection level**: one of three severity steps (1 warning, 2 and 3 protection) that each BCU protection can reach.

**protection word**: the 32-bit set of level-2 and level-3 bits a stack sends in CAN frame 0x700.

**protocol tag**: two-character marker at input register 2578 ("ZS") that selects the incremental protocol.

**protocol type**: the dongle's classification of an inverter's register layout, derived from the protocol code and tag.

**PSRAM**: external pseudo-static RAM used as extra heap.

**PT/CT ratio**: voltage and current transformer ratios used to scale meter readings.

**published hybrid map**: Sungrow's documented 13000-series register layout for hybrids.

**PV / ES variant**: the version of a grid profile for a PV-only inverter or for an energy-storage (hybrid) inverter.

**PV variant**: the set of grid-profile writes for PV-only inverters, chosen when input 5479 is 1 or 2.

**Q(U)**: volt-var reactive-power regulation.

**rdswitch**: the 24-hour "O&M mode" remote-maintenance window that gates the vendor develop login and the console log level.

**re-framing window**: SH10RS register range 21201–21999 whose requests the inverter forwards to the charger port.

**readResult**: status field of the dongle's reply to an iSolarCloud get/set-parameter command.

**recording file**: export that needs the inverter to fetch a record (fault wave, battery record) into its flash first.

**register**: one 16-bit Modbus value.

**register window**: a range of Modbus start registers that one handler serves.

**relay**: the inverter forwarding a device image onward (CAN to DSP/AFCI/battery, serial to optimizers).

**remote-control mask**: dongle bit field gating cloud command groups, whose bit 10 also gates the third-party upload.

**remote-maintenance window**: time-limited permission, opened by an admin or the cloud, that the develop role needs to log in.

**rest calibration**: resetting a module's SOC from its OCV after a long period without current.

**result_code**: the numeric outcome field in every local-API HTTP and WebSocket reply (1 = success).

**retained clear**: empty retained publish the dongle sends to remove a handled command from the broker.

**reverse tunnel**: an ngrok-style tunnel client the firmware can configure but never runs.

**ripple control**: utility signalling over the mains used in Germany for stepwise feed-in limits.

**RMII**: Reduced Media-Independent Interface between a MAC and an Ethernet PHY.

**role / privilege level**: the per-login access tier (visitor/user/admin/develop = 1/2/3/4).

**rollback**: the bootloader returning to the previous firmware when a new one fails its start-up check.

**ROM download mode**: the ESP32's built-in serial bootloader.

**rotation**: the controller's repeating cycle of block reads from the DSP, 0.6 to 0.8 s long.

**round**: one pass in which every due request of a device is queued and answered.

**route priority**: the rule that picks which interface carries the default route.

**RS-485**: a two-wire differential serial bus used between the dongle and the inverter.

**RSA signature**: a public-key signature proving that a package came from the holder of the private key.

**RSD**: rapid-shutdown device.

**RTU forwarding**: a mode in which the dongle's RS-485 port answers an external master instead of polling.

**RTU-slave mode**: configuration in which the dongle's RS-485 port answers an external Modbus master instead of polling the inverters.

**rundata**: the periodic cloud message carrying live values.

**sampling cycle**: spacing of the values written into a data file.

**SBR**: Sungrow's modular high-voltage LFP battery family for SH-RS hybrid inverters.

**search by port**: broadcast arbitration that collects serial, device code and address from every device on the bus.

**second-stage bootloader**: the ESP-IDF program that picks and starts an application slot.

**secure-boot signature**: the RSA signature at the end of each device image, checked by the inverter.

**secure-boot trailer**: the 432-byte RSA-3072/SHA-256 signature block appended to each device image, verified by the inverter.

**sentinel**: an all-ones (or 0x7FFF…) value meaning "no data".

**serial number**: the dongle's factory identifier, which is also its iSolarCloud identity.

**served register**: a value the inverter answers from its own copy of the CAN data.

**server switch**: cloud command 10384 point 10457 that moves a dongle to another broker.

**service map**: the BCU's own register window 19101-20000, reached through the CAN 0x1E0 tunnel.

**settings template**: the per-region file of grid profiles and installer settings held by the dongle and the app.

**settings-template family**: one of str_pv / res_es / res_new_pv / unit, picked from inverter type and protocol to drive country/grid-code writes.

**SG5.0RS**: Sungrow 5 kW single-phase residential string inverter of the SG-RS series.

**SGU**: Sungrow's signed firmware update container.

**shared credential**: a login or certificate that is identical on every dongle.

**sign**: a per-request SHA-256 the dongle computes from the server's clock and a baked-in secret, for the server to verify.

**signature trailer**: the RSA signature block appended inside a WiNet update package.

**slave 200-204**: the Modbus addresses at which the inverter answers for each battery stack.

**slave address**: the number a Modbus device answers to (1 to 246 on these devices; 0 is broadcast).

**slave binding**: the inverter's mapping of a stack serial number to a Modbus slave address 200-204.

**slaves 200–204**: Modbus addresses the inverter answers for the battery stacks.

**SmartConfig**: ESP-Touch v2 Wi-Fi provisioning, where credentials are broadcast as frame-length patterns heard in monitor mode.

**SMP**: symmetric multiprocessing, tasks run on either core.

**SNTP**: Simple Network Time Protocol, a UDP/123 time-sync client.

**SOC**: state of charge, how full the battery is in percent.

**SOC window**: the minimum and maximum SOC the inverter asks the battery to stay within.

**SOH**: state of health, remaining capacity relative to new.

**SP600**: the dongle firmware's name for the optimizer data model.

**SPI NOR**: serial NOR flash memory chip.

**SPLC**: power-line firmware inside each optimizer.

**stack**: one BCU and its modules, seen by the inverter as one Modbus slave.

**stack address**: the 0-3 number each BCU holds in FRAM and uses in its CAN identifiers.

**stage**: the mailbox state value (1 idle, 2 open, 3 failed, 4 success, 7 sub-module finished).

**staging area**: the dongle directory holding an upgrade file for an accessory to download.

**staging directory**: the external-flash folder (Update) where downloaded packages are held before verification.

**staging region**: flash area where a received image is stored before the boot loader installs it.

**staging window**: the area of the inverter's SPI flash where an image is assembled before checking.

**start-address routing**: choosing the handler from the first register only, ignoring the count.

**station**: the Wi-Fi client interface that joins the user's router.

**strapping pin**: an ESP32 pin whose level at reset selects a boot option.

**stream mode**: the inverter's CAN setting in which the bus carries one Modbus RTU tunnel instead of battery frames.

**string inverter**: an inverter that converts solar DC to household AC with no battery.

**SungrowSecureBoot trailer**: the 432-byte block at the end of an image carrying its RSA signature.

**supervisor**: an external watchdog chip on the WiNet-S2 that resets the board unless it is kicked.

**supervisor IC**: an external watchdog chip that resets the board if it is not fed in time.

**system block**: the master's aggregation of all stacks, sent as frames 0x000-0x01F.

**system frame**: a frame in 0x000-0x01F that the master stack sends with values aggregated over all stacks.

**system limit**: the current limit the master sends for all stacks together.

**task watchdog**: ESP-IDF timer that resets the chip when watched tasks stop running.

**temporary baud window**: inverter registers 21004 to 21018 that switch the line speed until silence or reboot.

**temporary-baud window**: inverter registers that switch RS-485 to a higher speed until a timeout or restart.

**third-party upload**: optional FTP/SFTP XML export of measurements to an owner-chosen server.

**time source**: dongle setting that selects which writer (web, NTP, cloud, Modbus-TCP) may set its clock.

**TLS**: the encryption layer under the cloud connection.

**TLV**: a type-length-value record; the .sgu body is a list of TLV parts.

**token**: 64-character session identifier returned by connect/login and echoed on every request.

**topic**: the named channel an MQTT message is published on.

**topology file**: JSON tree of strings and optimizer serial numbers that the dongle writes to the inverter.

**transmission cycle**: length of one upload window in minutes.

**transparent transmission**: SG5.0RS relay mode that sends requests for other slave addresses out of the dongle port.

**trust store**: the public key the dongle holds (publicKey.pem + publicKey.ver on external flash) used to authenticate updates.

**TXT record**: the key/value payload of an mDNS service advertisement.

**unit 247**: the dongle's own logger device, exposing its configuration and factory functions.

**unit id**: the Modbus device number in a request; on the dongle it is the device's position in its device list.

**update master**: the device that drives a transfer, here the inverter towards its modules.

**upgradeStatus**: the four-hex-digit per-device, per-file status the dongle reports to the cloud during a 10308 job.

**USB-Serial-JTAG**: the ESP32-S3's built-in USB console and debug port.

**variant**: one of the register sets of a grid-profile option, chosen by input 5479.

**version floor**: a firmware version built into the dongle against which incoming packages are compared.

**visitor**: the unauthenticated level-1 session a connect creates.

**VLAN**: a separate logical network segment on the same physical network.

**Volt-VAr / Q(U)**: reactive power as a function of grid voltage.

**Volt-Watt**: active-power reduction as a function of grid voltage.

**voltage mode**: balancing that compares each cell's voltage with the lowest cell.

**voltage-mode balancing**: bleeding cells that are more than a set voltage above the lowest cell.

**VPP**: virtual power plant, aggregated remote control of many systems.

**W5500**: WIZnet SPI Ethernet controller with built-in MAC and PHY.

**warning word**: the 32-bit set of level-1 bits sent alongside it.

**WebSocket**: a persistent two-way web connection, used by the dongle's web UI for live data.

**will**: the message the broker publishes on the dongle's behalf when its session drops.

**WiMeter**: Sungrow Wi-Fi meter accessory, product code 1819, that joins the dongle's hotspot.

**window**: a range of start addresses that one handler serves.

**WiProxy**: Sungrow Wi-Fi range accessory, product code 1820, with its own hotspot.

**wire address**: the 0-based address in a Modbus frame, one less than the documented register number.

**word order**: which of two registers holds the high half of a 32-bit value.

**YMODEM**: block-based file-transfer protocol (128- or 1024-byte blocks with CRC-16) that inverters use to send files raw over RS-485.

**YMODEM export**: raw file transfer of logs and records over RS-485.

**zlib**: the RFC 1950 compressed format used for cloud payloads.
