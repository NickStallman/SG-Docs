# WiNet-S versus WiNet-S2

The [WiNet-S](winet-s.md) (ESP32, product V200) and the [WiNet-S2](winet-s2.md) (ESP32-S3, product
SV300) run one firmware code base built for two chips. Almost everything a user, an installer or a
program on the network sees is the same. The differences are the chip and its pins, the Ethernet
hardware, the watchdog strategy, memory sizes, version constants, and a
handful of device-support features that exist on only one model.

## Summary table

| Aspect | WiNet-S | WiNet-S2 |
|---|---|---|
| Product code in firmware strings | `WINET-SV200.001.00.Pnnn` | `WINET-SV300.001.03.Pnnn` |
| Project / product name | `WiNet-S` | `WiNet-S2` |
| Cloud product type | `0x715` (1813) | `0x720` (1824) |
| SoC | ESP32 (dual-core LX6) | ESP32-S3 (dual-core LX7) |
| Internal flash | 16 MB | 16 MB |
| PSRAM | quad, 80 MHz, size not known | 8 MB octal, 80 MHz |
| External SPI NOR | 16 MB | 32 MB |
| Second-stage bootloader offset | `0x1000` | `0x0` |
| `oemdata` (web UI) partition | 4 MB | 5 MB |
| Ethernet | internal EMAC + DP83848 RMII PHY | W5500 on SPI3 |
| Ethernet MAC address | eFuse Ethernet MAC, no override | stored MAC if set, else derived locally administered MAC |
| RS-485 TX / RX / DE | GPIO13 / 35 / 12 | GPIO18 / 17 / 8 |
| External flash pins (MOSI / MISO / SCLK / CS) | 2 / 34 / 14 / 15 on HSPI, Fast Read | 11 / 13 / 12 / 10 on SPI2, DIO |
| LEDs (red / blue / green) | GPIO4 / 32 / 33 | GPIO19 / 38 / 20 |
| Button | GPIO36, external pull-up (probably) | GPIO4, internal pull-up |
| Console UART0 | GPIO1 / GPIO3 | GPIO43 / GPIO44 |
| USB | none on the chip | present on the chip, pins used by LEDs |
| Hardware watchdog | chip timer-group watchdog fed from the monitor loop | external supervisor IC (GPIO2 enable, GPIO7 kick, 500 ms) |
| Identity file name | `dev_file_247_1813_2` | `dev_file_247_1824_2` |
| Version floor for updates (oldest installable) | P026 | P012 |
| Signature-trailer rule for update packages | none | required at or above a baked version floor (error 4104) |
| Remote-control permission mask default | `0x4EF` | `0x4FF` |
| Gateway ping watchdog | none | after a DNS timeout on Wi-Fi, pings the Wi-Fi gateway 3 times and restarts if none answer (at most once per DNS outage) |
| Online debug log stream | present | absent |
| Topology, SP600 optimizer support, incremental battery scan, Wi-Fi mass provisioning | absent | present |

## What is identical

### Protocols and data

- **RS-485 master**: the same Modbus RTU master, the same discovery sequence, the same per-device
  register windows and the same inter-frame gaps
  ([../protocols/rs485-master.md](../protocols/rs485-master.md),
  [../protocols/device-discovery.md](../protocols/device-discovery.md)).
- **Register maps**: a property of the inverter, battery or meter, not of the dongle. Both dongles
  read and write the same registers on the same device types
  ([../appendix/register-tables/rs485-inverters.md](../appendix/register-tables/rs485-inverters.md)).
- **Internal data model**: the same per-device configuration and measurement points, the same
  persistence on the external flash, the same default values apart from model and version
  strings.

### Local access

- The same HTTPS web server and WebSocket API, the same routes, the same login scheme ([../protocols/web-api.md](../protocols/web-api.md)).
- The same Modbus TCP server, unit mapping and source-address allow-list
  ([../protocols/modbus-tcp.md](../protocols/modbus-tcp.md)).
- The same Wi-Fi hotspot (`SG-<serial>`, 11.11.11.1/24, open, two clients), SmartConfig, mDNS
  advertisement and button gestures ([leds-and-button.md](leds-and-button.md),
  [../protocols/local-services.md](../protocols/local-services.md)).

### Cloud

- The same iSolarCloud MQTT broker set, topic grammar, JSON messages, zlib-format payload
  compression, TLS settings and shared broker credential, with the dongle serial number as the
  client identity ([../protocols/cloud-mqtt.md](../protocols/cloud-mqtt.md),
  [../protocols/cloud-messages.md](../protocols/cloud-messages.md)). Only the product type and
  product name in the published identity differ.

### Firmware updates

- The same SGU container and the same signature scheme (MD5 and SHA-256 digests, RSA-2048
  signature) checked against a public key stored on the external flash, the same update
  channels, and the same handling of the web UI image
  ([../firmware-update/sgu-container.md](../firmware-update/sgu-container.md),
  [../firmware-update/update-channels.md](../firmware-update/update-channels.md)). The two models
  differ only in their version floors and in the S2's signature-trailer rule.

### Platform

- Both chips are dual-core and run FreeRTOS in SMP mode at a 1000 Hz tick, with every application
  task free to run on either core.
- Both put most heap allocations in PSRAM.
- The boot order, the 3 s monitor loop, the 30 s OTA validation, the partition table layout (apart
  from `oemdata` and `usrdata` sizes), the LittleFS mount and the factory reset are the same
  ([boot-and-runtime.md](boot-and-runtime.md), [flash-layout.md](flash-layout.md)).
- The LED patterns and their meanings are the same; only the GPIOs differ.

## What differs

### Chip and pins

The WiNet-S uses a classic ESP32, which forces some awkward pin choices: input-only GPIO34–36 for
receive lines and the button, and five strapping pins carrying live signals (GPIO0 as the RMII clock
input, GPIO12 as RS-485 DE, GPIO2/15 for the external flash, GPIO5 for the PHY reset). The WiNet-S2
uses none of the ESP32-S3 strapping pins. Its trade-off is that the USB pins (GPIO19/20) drive LEDs,
so its USB port is unusable. Full pinouts are on the two board pages.

### Ethernet

| | WiNet-S | WiNet-S2 |
|---|---|---|
| Hardware | ESP32 EMAC, DP83848 PHY over RMII, 50 MHz clock into GPIO0, PHY address 1 | WIZnet W5500 over SPI3 at 36 MHz, interrupt on GPIO6 |
| Reset line | GPIO5 to the PHY | GPIO5, probably to the W5500 |
| Link-up IPv6 link-local creation | failure ignored | failure aborts the firmware |
| Ethernet driver stop | no effect on open sockets | Ethernet sockets are closed and listeners reopened |

The configuration model above the driver (DHCP default, static address applied at once, IPv6
addresses, link status reporting, stop/start around a firmware update) is the same.

### Watchdog

The WiNet-S relies on the ESP32's main timer-group watchdog, fed from the application's 3 s monitor
loop, and on the ESP-IDF task watchdog for the idle tasks. The WiNet-S2 instead drives an external
supervisor IC: enable on GPIO2, a 500 ms kick on GPIO7, switched off before an update restart and on
brownout ([winet-s2.md](winet-s2.md#external-supervisor)). The S2 also uses the task watchdog for
the idle tasks.

The S2 also has a Wi-Fi-only recovery check: when a DNS lookup times out while the station is
joined, it pings the Wi-Fi gateway three times, 20 s apart, and restarts if none of them answers. It
does this at most once per DNS outage
([../protocols/local-services.md](../protocols/local-services.md#icmp-gateway-ping-watchdog-winet-s2-only)).
The WiNet-S has no such check.

### Memory

The WiNet-S2 has twice the external flash (32 MB versus 16 MB) and its LittleFS volume has twice the
blocks (`0x2000` versus `0x1000`). Both boards run their PSRAM at 80 MHz, the same clock as the
internal flash: the WiNet-S2 has 8 MB of octal PSRAM, the WiNet-S a quad PSRAM whose size is not
known. The 4G APN text fields also differ
in size (150/127/127 bytes on the S, 128/64/64 on the S2), although neither board has a modem.

### Identity and version constants

Each build hard-codes its product type in the cloud identity and in the name of the identity file on
the external flash. At boot the dongle deletes an identity file whose name carries the other model's
product type, so an external flash moved between models loses its identity. Each build also refuses
update packages older than its own floor (P026 on the S, P012 on the S2). The S2 additionally
requires the in-file signature trailer on packages at or above a version floor built into the
firmware, and rejects such a package without the trailer with error 4104
([../firmware-update/sgu-container.md](../firmware-update/sgu-container.md)).

### Remote-control mask

Both models keep a configuration value that masks which cloud remote-control functions are
allowed; the factory default is `0x4FF` on the S2 and `0x4EF` on the S. Remote-control commands are described in
[../protocols/cloud-commands.md](../protocols/cloud-commands.md).

### Features present on one model only

| Feature | Model | Description |
|---|---|---|
| Online debug log stream | WiNet-S only | a plain-text TCP log stream, started on demand by a Modbus TCP configuration write, not at boot ([../protocols/diagnostics-and-log-export.md](../protocols/diagnostics-and-log-export.md)) |
| Topology settings | WiNet-S2 only | system topology configuration |
| Optimizers / MLPE | WiNet-S2 only | handling of module-level power electronics ([../devices/optimizers-and-mlpe.md](../devices/optimizers-and-mlpe.md)) |
| Incremental battery scan | WiNet-S2 only | a battery scan that probably adds newly found battery modules without a full rescan |
| Wi-Fi mass provisioning | WiNet-S2 only | distribution of Wi-Fi network settings to several devices ([../protocols/setup-and-provisioning.md](../protocols/setup-and-provisioning.md)) |
| HTTP on port 80 | WiNet-S2 only | a plain HTTP listener that starts only when a WiMeter is in the saved device list at boot ([../devices/wimeter-and-wiproxy.md](../devices/wimeter-and-wiproxy.md)) |

### Wi-Fi details

The hotspot and station logic is the same, with two small differences. After starting Wi-Fi the
WiNet-S always tries to connect as a station, while the S2 tries only when a station SSID is
stored. In network-config mode (button held for more than 5 s) the WiNet-S also scans for nearby
`SG-` hotspots with WPA security and joins one, which is how it pairs with a WiProxy; the S2 only
sets the mode flag and turns its own hotspot on.

## Bluetooth

Neither dongle uses Bluetooth. Both chips have a Bluetooth-capable radio (Classic and BLE on the
ESP32, BLE on the ESP32-S3) that shares the Wi-Fi antenna, but neither firmware starts a Bluetooth
controller or contains a Bluetooth host stack. Setup, maintenance and firmware updates use Wi-Fi, Ethernet and the
hotspot only. Bluetooth does appear elsewhere in the Sungrow system: the iSolarCloud phone app can
talk to an SG5.0RS inverter through a Bluetooth-to-serial bridge on the inverter's own service
port, without the dongle being involved ([../devices/sg5rs/architecture.md](../devices/sg5rs/architecture.md)).
