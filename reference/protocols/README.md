# Protocols

## RS-485 (dongle to inverter)

- [RS-485 master](rs485-master.md): timing, polling model, request building, poll classes, capability gating.
- [Device discovery](device-discovery.md): how the dongle finds the inverter, and the baud switch to 115200.
- [Function codes](modbus-function-codes.md): the private function codes, above all the 0x65 change stream.
- [Writes and meter sync](writes-and-meter-sync.md): how parameter writes reach a device, and the meter-sync feature.

## Local network

- [Modbus TCP](modbus-tcp.md): ports 502 to 516, TLS, allow-lists, unit ids, the logger registers and their hazards.
- [Web API](web-api.md): HTTPS and WebSocket on 443, sessions, roles, the service catalogue and the HTTP routes.
- [Local services](local-services.md): mDNS, SNTP, DHCP/DNS, the ping watchdog, IEC-104 slots, client links.
- [Setup and provisioning](setup-and-provisioning.md): SmartConfig, the hotspot, mass provisioning, installer roles, settings templates.
- [Remote access](remote-access.md): the remote-maintenance switch, the reverse tunnel, debug streams, log export.
- [Regional and legacy features](regional-and-legacy-features.md): features for other markets and leftovers from older products.

## Cloud (iSolarCloud)

- [MQTT link](cloud-mqtt.md): brokers, TLS, identity, session, topic grammar.
- [Envelope](cloud-envelope.md): the JSON envelope, chunking, compression.
- [Messages](cloud-messages.md): everything the dongle publishes, with cadence and payload shape.
- [Commands](cloud-commands.md): everything the cloud can ask the dongle to do.
- [Diagnostics and log export](diagnostics-and-log-export.md): I-V curve scans, YMODEM transfers, log export, fast collection.
- [Third-party upload](third-party-upload.md): the periodic XML push to a server of your choice.
