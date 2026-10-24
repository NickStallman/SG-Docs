# Other LAN-facing services

Besides the Modbus-TCP slave ([modbus-tcp.md](modbus-tcp.md)) and the HTTPS web
API ([web-api.md](web-api.md)), the dongle exposes a handful of other services on
the local network. Most are advertised or configurable but never actually accept
a connection. This page covers them one at a time.

"The dongle" means either WiNet-S or WiNet-S2; differences are called out.

## Port summary

| Port(s) | Protocol | Status |
|---|---|---|
| 443 | HTTPS web API + WebSocket | listens (always) |
| 80 | plain HTTP (WiMeter upload + static files) | conditional (WiNet-S2 only, and only when a WiMeter is in the saved device list at boot) |
| 502 | Modbus-TCP | listens when enabled (region dependent; see [modbus-tcp.md](modbus-tcp.md)) |
| 503–516 | Modbus-TCP (extra configurable listeners) | listen when enabled (each of the 15 listeners 502–516 has its own switch; 516 is the TLS listener) |
| 2404–2418 | IEC 60870-5-104 server slots | configurable only (never bound) |
| 5353/UDP | mDNS responder | listens (always) |
| 123/UDP | SNTP client (outbound) | conditional (off by default; see below) |

The dongle also opens outbound TCP connections of its own (cloud uplink, and the
configurable client links in the sections below). Those are client sockets, not
listeners.

## mDNS

Both dongles run an mDNS responder and publish exactly **one** service:

- Instance name: `WiNet-WebServer`
- Service type: `_http._tcp`
- Port: **80**
- Host name: `SUNGROW<serial>.local`
- Instance description: `winet home web server`

The service is added once at start-up and never removed. **Port 80 is advertised
unconditionally**, even though the plain HTTP server on port 80 normally does not
exist (it runs only on a WiNet-S2 that had a WiMeter in its saved device list at
boot; a WiNet-S has no plain HTTP listener at all). So on a normal unit the mDNS
record points at a closed port.

### TXT records

The TXT data is the dongle's identity and device list, as semicolon-separated
strings. At start-up it carries only a `board` key plus `path=/`. The first time
the refresh runs (on a network or device-list change) it is replaced by a
seven-key set: `board`, `inverter`, `ethinfo`, and `battery1` to `battery4`
(battery keys only appear for batteries that exist). All values are ASCII, fields
separated and terminated by `;`.

| Key | Format | Fields |
|---|---|---|
| `board` | `SUNGROW;1;<product>;<serial>;<flag>;` | vendor, a constant `1`, product (`WiNet-S` / `WiNet-S2`), dongle serial, and the Modbus-TCP-on-516 TLS flag |
| `inverter` | `<unit addr>;<dev code>;<serial>;1;516;<model>;<phys addr>;<machine type>;` | inverter unit address, device code, serial, a constant `1`, the constant `516`, model string, physical address, and the "machine type" input register (5479) |
| `battery1`–`battery4` | `<unit addr>;<phys addr>;<dev code>;<serial>;1;516;<model>;` | one entry per battery stack |
| `ethinfo` | `<eth0 IP>;` | the wired IP when the Ethernet link is up, otherwise just `;`. Wi-Fi addresses are never published here. |

The record therefore discloses, to anyone on the LAN who sends an mDNS query and
with no authentication, the serial numbers of the dongle, the inverter and every
battery, the inverter model and device code, the "machine type", and (with
Ethernet up) the dongle's wired IP address. The constant `516` is the default
Modbus-TCP-over-TLS port; it does not track the configurable port or the enable
flag, so after a port change or with that listener disabled the advertised value
is wrong.

No consumer of the record is known. The iSolarCloud phone app does not browse
mDNS (its local mode joins the dongle's own hotspot, so it needs no discovery).
The field layout fits a LAN Modbus-TCP-over-TLS client that finds devices by mDNS,
such as a vendor PC or installer tool, but none has been identified.

## SNTP client (UDP 123)

The dongle has one system clock and several possible sources for it, selected by a
"time source" setting (default: iSolarCloud). **SNTP is only one of those sources
and it is off by default.** With the factory setting the SNTP thread is never
started; the clock comes from the cloud's time broadcast instead. The NTP host,
port and interval shown on the System time page (`time.cloudflare.com`, port 123,
5-minute interval) are stored defaults, not proof that SNTP is running.

SNTP runs only when:

- the time source is set to one of the SNTP values (at boot), or
- the NTP fields are written from the web page (which enables SNTP until the next
  reboot or the next change of time source).

When it runs, one sync is a periodic one-shot: every interval the dongle starts the
lwIP SNTP client for one short run, bound to one interface,
waits up to 100 s for the first reply, then stops. It tries the interfaces in the
order Wi-Fi station, Ethernet, access point and stops at the first success. The
configured port is stored but never used — the client always talks to UDP 123.

If the clock is still invalid and no network source has set it, the dongle falls
back to reading the inverter's clock registers (holding registers 5000–5005) and
uses that time. The cloud set-time command is described in
[cloud-commands.md](cloud-commands.md).

> Modbus addresses on this page use the 1-based numbering of Sungrow's published
> Modbus documentation (wire address = doc address − 1).

## DNS and DHCP

There is no separate vendor DNS client: the dongle uses the standard lwIP resolver
over UDP 53. DNS servers come from DHCP (the default) or from a persisted static
setting on the Ethernet side; Wi-Fi is DHCP only. The dongle sets no DHCP
hostname, so it appears on the network under the stack default name.

A name lookup in the cloud/connection path is asynchronous with a fixed 10 s wait:
the dongle starts the lookup and polls once a second for 10 s. If no address has
arrived it logs the configured DNS servers (diagnostic only — it does **not** retry
through them), records a link-failure reason and gives up. A negative answer
(NXDOMAIN) is not distinguished from a timeout, so it also costs the full 10 s.

When both Ethernet and Wi-Fi are up, Ethernet wins the default route.

A boot that was told to open SmartConfig cancels that listener as soon as a wired
interface obtains a real (non-link-local) DHCP lease; see
[setup-and-provisioning.md](setup-and-provisioning.md).

## ICMP gateway-ping watchdog (WiNet-S2 only)

The WiNet-S2 has a Wi-Fi-only self-recovery watchdog. When the station is joined
to a Wi-Fi network and a DNS lookup for a TCP server (normally the iSolarCloud
broker) has timed out after 10 s, the dongle pings its **Wi-Fi gateway** three
times (ICMP echo, 20 s apart, 1 s reply timeout each). If all three get no reply,
it logs `PING FAILED(...)`, waits 2 s and reboots itself.

Details:

- Only the Wi-Fi gateway is pinged, not a cloud host or a DNS server. A
  dongle on Ethernet only, or one whose station is not joined, never pings.
- A persistent marker is written to flash just before the reboot. After the
  restart it blocks any further ping until a DNS lookup succeeds again, so the
  watchdog reboots **at most once per DNS outage**. A dongle whose gateway ignores
  ICMP and whose DNS stays broken reboots once and then stays up.
- Total time from the start of a failing lookup to the reboot is about 72 s
  (10 s DNS + roughly 60 s of pinging + 2 s).
- The echo requests leave with an IP TTL of 0. Most hosts answer
  a ping to their own address regardless of TTL, but a router or firewall that
  drops TTL-0 packets would make every session fail.

The WiNet-S has no such watchdog. It carries the same ping library but only a
factory self-test uses it.

## IEC 60870-5-104 (TCP 2404–2418)

The web UI offers 15 IEC 60870-5-104 **server** links for SCADA masters, on ports
2404 to 2418, each with its own enable flag and certificate import. **The dongle
never opens a socket on any of these ports.** The enable setting is stored,
but the dongle's listener code accepts only Modbus-TCP, so the request to open the
port always fails and nothing is bound. A master connecting
to 2404 gets a refused connection (TCP RST). The firmware contains no IEC-104
protocol code at all — no STARTDT, APDU, ASDU, interrogation or type handling.

A dongle may report `iec104_svr1` as "enabled" on 2404 in the web UI; that is only
the stored configuration value. The port is not listening.

The port number the UI shows for each server is a constant in the firmware, not a
value the dongle reads back, so it is informational only.

## Outbound TCP client links

The "Communication settings" page lets an operator add outbound links on which the
**dongle connects out** to a host and port typed into the UI:

- **Modbus-TCP client links** (`mb_tcp_cli1..5`). Despite the name, a Modbus-TCP
  client link is a *reverse-connected slave*: the dongle opens the TCP connection
  (useful behind NAT or a firewall) and the remote host then acts as the Modbus
  master and polls it. The dongle serves these through the same Modbus-TCP slave
  engine as the :502 listeners, including unauthenticated writes, and never sends a
  request of its own.
- **IEC-104 client links** (`iec104_cli1..5`). These connect but do nothing useful:
  no part of the firmware handles data arriving on the link, and the dongle never
  sends STARTDT. They have a settings UI, a
  point-table export and persisted settings but no protocol engine.

Behaviour worth knowing:

- Only the WiNet-S can add a client link from its UI; the WiNet-S2's add button
  reaches no handler, so nothing is created there (it can still be created by the
  boot restore of an already-enabled slot). An enabled client link reconnects after
  every reboot.
- Both kinds inherit the cloud TLS flag (default: TLS on) and the cloud CA file.
  A client link therefore asks for TLS and verifies the remote certificate against
  the Sungrow cloud CA. Against a plain Modbus-TCP or IEC-104 host the handshake
  fails and the link retries forever without connecting. A plain-TCP client link is
  used only when the remote port is 19999.
- All links share one 15-slot link list with the cloud link and with accepted
  inbound connections; inbound Modbus-TCP connections are refused once the list
  holds 4 or more records, so a few enabled client links plus the cloud link can
  block every inbound Modbus-TCP connection.

A separate "Fupin" link uses the same machinery for a Chinese
reporting platform; it cannot be enabled on these firmware versions and is covered
in [regional-and-legacy-features.md](regional-and-legacy-features.md).

## Adding a LAN device by IP:port (Modbus-TCP master)

The dongle can poll a device that lives on the LAN or Wi-Fi instead of on RS-485.
The operator gives an IP address and a TCP port (web `POST /device/add` with
`protocol=MODBUS-TCP`), and the dongle opens a TCP client link to that address and
polls the device as a Modbus-TCP **master**. Up to 15 network addresses can be used per
protocol, within the overall device limit (11 devices on a WiNet-S, 15 on a
WiNet-S2).

The frames the dongle sends to these LAN devices are **not plain Modbus-TCP**. Each
RTU frame is padded with `0xFF` to a multiple of 16 bytes, AES-128-ECB encrypted
with a fixed shared key baked into the firmware, and sent after a 6-byte header
whose protocol-id field is the ASCII text `AE`. A standard third-party Modbus-TCP
slave therefore cannot be polled by the dongle. The only models the UI offers
on a network port are three vendor meters (DTSD1352, DTSU666, S100) plus the WiMeter
and WiProxy accessories. The accessories' own management traffic (clock push, log
export) uses a plain MBAP header instead, so a WiMeter sees both framings on one
link.

Adding an IEC-104 device over the LAN creates a device node and a TCP connection,
then does nothing: the device is never polled.
A "scan by device type" over the LAN is not supported (`ETH1 not support`); only
RS-485 ports can be scanned.

For DL/T 645 meters, which can be named in the add dialog but never actually
polled, see [regional-and-legacy-features.md](regional-and-legacy-features.md).

## Port 80 WiMeter upload endpoint

On a WiNet-S2 that has a WiMeter saved in its device list at boot, a plain HTTP
server runs on port 80 with three handlers:

- `POST /device/addWiMeter` — the "add a device" request, without the login-token
  check that the HTTPS `/device/add` route has. The most likely caller is the
  WiMeter re-registering its own IP and serial.
- `POST /upload/*` — the receive side of the WiMeter/WiProxy log export. It stores
  one uploaded file (up to about 8 MB) under a fixed path.
- `GET /*` — static file handler for the web UI.

Neither POST route checks any credential. Anyone who can reach port 80 can use
them while the server is up. A failed upload can delete a file inside the web-UI
filesystem, because the handler cleans up using the request URI rather than the
fixed output name. A WiProxy (as opposed to a WiMeter) does not
cause port 80 to open, so a WiProxy-only unit cannot complete its log-export jobs.
The WiNet-S has no plain port-80 server; it registers these two routes on its
single HTTPS server instead.
