# Notable findings

Most of this repository describes ordinary engineering: frames, registers and timers that do
what you would expect. This page collects the things that are new, surprising or worth knowing
before you build on these devices. Each one is a short summary with a link to the page that has
the detail.

Register numbers are the 1-based numbers used in Sungrow's Modbus documentation. On the wire the
address is one less.

## Battery

### The SBR balancing rules

An SBR192 balances only when the stack has rested for more than 29 minutes below 0.51 A, and
checks at most every four minutes. It then bleeds cells that are 35 mV or more above the lowest
cell until they are within 15 mV of it. It never bleeds a cell below 3.100 V, and it skips
balancing when the modules are 10 °C or more apart, the hottest is above 44.99 °C or the coldest
is at or below 0 °C. On a sunny day balancing therefore starts about 40 minutes after the battery
reaches 100 % and runs for 30 to 70 minutes. A house that draws from the battery in the late
afternoon may hardly ever see it. Register 10683 shows the status. See
[Inside the battery](08-inside-the-battery.md#balancing-a-sunny-day) and
[balancing and SOC](../reference/devices/sbr192/balancing-and-soc.md).

### The battery list depends on every stack answering

The dongle learns the battery slave addresses from the hybrid inverter and probes each one.
It adds the batteries to its device list only when **every** probed stack has answered. One
silent stack, for example one that is switched off, keeps all of them out of the list, the web
UI and the cloud upload. See [device discovery](../reference/protocols/device-discovery.md).

### Register 5673 carries only half of the total battery charge

On the SH10RS, register 5673 serves only the low 16 bits of the 32-bit lifetime battery-charge
total, and its partner 5674 is never filled. A true total of 15 751.7 kWh therefore reads as
2 644.5 kWh, and the value wraps back to near zero every 6 553.6 kWh. The full value is
served correctly at 13041/13042. The discharge total at 5670/5671 is not affected. See
[SH10RS battery interface](../reference/devices/sh10rs/battery-interface.md).

## Inverter

### The 0x65 change stream

Both inverters answer a private Modbus function code, 0x65, that reports only the registers whose
value has moved beyond a per-register deadband since they were last reported. The stream tracks
about 740 registers on the SH10RS and 667 on the SG5.0RS; after one full transfer, each request
returns only what has changed. That is how the dongles keep up with so much data over a 9600 baud line. The price is a little
staleness. On the SH10RS the power registers have a 50 W deadband, so the dongle's power figure
can lag by up to 50 W. See [Modbus function codes](../reference/protocols/modbus-function-codes.md).

### Reads that hold the inverter's ports, and the register that makes it permanent

Requests that start in certain address ranges (16000–17500, 29500–29899 and, on the SH10RS,
36702–38999) are forwarded to the power-stage DSP. For each one the inverter stops serving its
other Modbus ports for at least 1.5 s (SH10RS) or 10 s (SG5.0RS), even after the DSP has answered.
On the SH10RS a multi-register write of 170 to register 37004 makes that hold effectively endless
until another value is written there or the inverter restarts. The dongles never poll these
ranges. Cloud pass-through and Modbus TCP clients can reach them. See
[Inside the inverters](07-inside-the-inverters.md#why-some-reads-are-slow) and
[SH10RS register windows](../reference/devices/sh10rs/register-windows.md).

### Meter sync can take over a hybrid's grid reading

When a Modbus meter is attached to the dongle, the dongle repeatedly writes that meter's
readings into the inverter (16 registers at 33519). For 60 s after each write the inverter ignores
the meter on its own meter port. On a hybrid whose meter is wired to the inverter, those writes
would replace the reading behind the export limit and the battery control with a partial, slower
copy. See [writes and meter sync](../reference/protocols/writes-and-meter-sync.md).

### Inverter firmware updates pass through the controller

The dongle hands every inverter image to the inverter's communication controller. The controller
relays the power-stage DSP and arc-fault board images over its internal CAN bus, and battery
images over the battery CAN bus. While the DSP image is relayed, the inverter stops producing
power, so a DSP update on a sunny day costs generation. See
[device firmware relay](../reference/firmware-update/device-firmware-relay.md).

## Dongle

### The WiNet-S2 refuses unsigned packages above a version floor (error 4104)

The WiNet-S2 compares the version inside an update package with a version floor built into its
firmware. If the package claims that version or newer, it must carry the signature trailer inside the file.
Without it the dongle rejects the package with error **4104** ("upgrade check error"). Packages
below the floor skip this check. They still need the separate detached signature,
and they are refused as a downgrade unless downgrades are allowed. The WiNet-S has no such
check and accepts packages with or without the trailer. See
[SGU container](../reference/firmware-update/sgu-container.md) and
[update channels](../reference/firmware-update/update-channels.md).

### IEC 60870-5-104 can be configured but never listens

The web UI offers fifteen IEC-104 server links on TCP ports 2404–2418, and server 1 often shows
as enabled. The dongle never opens any of those ports, because no part of the firmware
accepts IEC-104 traffic, and a SCADA master that connects is refused. The "network device" form
that would add an IEC-104 device is also a dead end. See
[regional and legacy features](../reference/protocols/regional-and-legacy-features.md).

### DL/T 645 meters cannot work

The web "add device" form can show a DL/T 645 meter address field, and the serial receiver
understands the frame format. Nothing in between does: the dongle silently ignores the add
request, so the browser waits about 30 s and gets error 209. The dongle never sends a
DL/T 645 request. The meters that do work on the dongle's RS-485 bus are all Modbus devices. See
[meters](../reference/devices/meters.md).

### mDNS advertises a web server that is not there, with serial numbers attached

Both dongles announce themselves on the LAN as `SUNGROW<serial>.local` with an `_http._tcp`
service on port 80. The service's TXT records carry the serial numbers of the dongle, the
inverter and every battery, plus the inverter model. Any device on the network can read them
without logging in. Port 80 is closed on the WiNet-S and on a WiNet-S2 without a paired WiMeter.
See [local services](../reference/protocols/local-services.md).

### One Modbus TCP write factory-resets the dongle

The dongle answers Modbus TCP requests for unit 247 itself. Writing any value to its 1-based
register 39098 (wire address 39097) wipes the dongle's settings and reboots it. There is no
password and no confirmation. The client even gets a normal reply first. The same unit can change
the serial number, the cloud host and, on the WiNet-S2, the Ethernet MAC address. See
[Modbus TCP](../reference/protocols/modbus-tcp.md) and
[Security and privacy](09-security-and-privacy.md).

### The web UI allows one session at a time

Every new WebSocket `connect` to the web UI logs out every other connection, before any password
has been checked. A second browser tab, a phone app or a monitoring script polling the local API
silently evicts the person who is logged in. Logging in also evicts any other session with the
same role. See [web API](../reference/protocols/web-api.md).

### The button's factory reset takes 30 s, not 5 s

Holding the button for more than 5 s enters network-configuration mode. Only after 30 s does the
dongle factory-reset. The device log labels the factory-reset branch with "5 s", which does not
match the behaviour. See [LEDs and button](../reference/hardware/leds-and-button.md).

### A failed mount formats the external flash

The dongle keeps its identity, keys, certificates, settings and logs on a separate SPI NOR chip
formatted with LittleFS. If that filesystem ever fails to mount, the dongle firmware erases and
re-creates it, and the serial number, keys and certificates are gone with it. The firmware accepts
only on-disk version 2.0, so a tool that writes the volume with a newer LittleFS (disk version 2.1) can
trigger exactly that on the next boot. See [flash layout](../reference/hardware/flash-layout.md).

### Two dongles, two ways to do Ethernet

The WiNet-S (ESP32) uses the chip's built-in Ethernet MAC with a DP83848 PHY over RMII, clocked
from an external 50 MHz reference on GPIO0. The WiNet-S2 (ESP32-S3, which has no Ethernet MAC)
uses a WIZnet W5500 Ethernet controller on its own SPI bus. That changes the pin map and how the
MAC address is set. See
[WiNet-S versus WiNet-S2](../reference/hardware/winet-s-vs-s2.md).

### Every dongle shares the same cloud credentials

The credentials a dongle uses for its MQTT connection to iSolarCloud are not unique per device.
The same built-in values are used by every dongle. See
[cloud MQTT](../reference/protocols/cloud-mqtt.md) and
[Security and privacy](09-security-and-privacy.md).

### The WiNet-S2 has a hardware watchdog that must be kicked every 500 ms

The WiNet-S2 board has an external supervisor chip. At start-up the firmware holds its enable
line (GPIO2) low, toggles the kick line (GPIO7) ten times at 100 ms, then arms it and toggles the
kick every 500 ms from a timer. If the toggling stops, the board is reset (the timeout is not known).
The firmware disarms it only before the restart that ends an update and in the brownout handler. The WiNet-S has no such part. See [WiNet-S2](../reference/hardware/winet-s2.md) and
[boot and runtime](../reference/hardware/boot-and-runtime.md).
