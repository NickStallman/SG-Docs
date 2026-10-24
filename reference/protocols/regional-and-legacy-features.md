# Regional and legacy features

A short page for features that exist in the firmware for a particular market or as
leftovers from an earlier product, and that do little or nothing on a WiNet-S or
WiNet-S2 as shipped.

## Chinese poverty-alleviation reporting ("Fupin")

"Fupin" (扶贫, "poverty alleviation") is a Chinese national PV monitoring platform.
The web UI has a settings page for it and the firmware has one outbound TCP link
that would report to it, with a default server of
`cie-bj.tpddns.cn:19020`.

**The feature cannot be switched on in these firmware versions.** The dongle's
transmit web routes do not recognise the link's name, so they answer "protocol not found", and the enable setting has no other writer
(not Modbus, not cloud, not the boot restore). Even if it were enabled, no part of
the firmware handles data on that link, so the dongle would never send: the link would connect (TLS by default, verified against
the cloud CA) and then stay silent. The settings the page collects (user name,
password, device serial, plant code, start address, certificate) are read by no
code. The link is off by default.

## Japan utility output-schedule (Kyushu Electric)

The firmware carries a dormant "power-plant dispatch" settings group aimed at the
Japanese output-control scheme, in which the utility publishes a curtailment
schedule the plant downloads. It includes a schedule URL defaulting to
`https://re-ene.kyuden.co.jp/scheduleSend/`, a plant code, plant and component
capacities, a certificate name and a Japan-specific NTP host.

**No code opens a connection to Kyushu Electric.** The URL and most of the group
exist only as stored defaults; nothing reads the URL or builds a request from it.
Of the whole group only two fields are ever touched by code — a "dispatching mode"
and the Japan NTP host — and only by the System time web page. The Japan NTP host
is stored and shown but never actually used as an NTP server (the client always
uses the ordinary NTP host). The `/dispatch/*` web routes do not exist; they are
privilege-table rows with no handler.

One related flag does change behaviour: a persistent "Japan time enable"
setting. When it is 1, the dongle refuses the cloud's set-time and set-timezone
commands and a Modbus-TCP clock write, and at boot it skips applying the stored
time zone and skips starting NTP. It defaults to 0, and no code in either image
writes it, so it can only be 1 if the stored device file already holds 1 — which
would be the case only on a Japan-market build or a unit provisioned by a factory
tool.

## 4G modem leftovers

Neither dongle has a cellular modem: there is no AT-command code, no modem UART and
no PPP session the application uses. What remains is leftover code from an earlier
Linux-based WiNet-4G logger:

- Web routes `/4g/list` and `/4g/update` (plus IPv6 variants on the WiNet-S2) that
  read and write a few stored APN text fields, which nothing applies to a modem.
- Logger values for cellular signal strength, operator, details, ICCID and (on the
  WiNet-S2) a 4G IPv6 address — defined in the tables but never written, because
  there is no modem to produce the values.
- A Wi-Fi/4G "data sent" check that runs every 5.2 s. Its Wi-Fi half is live (it is what drives the blue LED's
  "data sent" blink), but the 4G half is never true, because no network interface is
  a cellular one.
- A LED-pin opener that tries to open Linux-style GPIO device files for a "wifi" and a "4g"
  LED; the opens always fail on this hardware and only log an error.
- A firmware-version reader that looks for a Quectel modem's version file, which
  does not exist here.

So the cellular LED code and state message exist, but there is no modem and no PPP.

## Bluetooth

Neither dongle has Bluetooth. See
[../hardware/winet-s-vs-s2.md](../hardware/winet-s-vs-s2.md).

## DL/T 645 meters

DL/T 645 is the Chinese electricity-meter serial protocol. The firmware has three
of the four pieces needed to use one and is missing the fourth:

- The RS-485 receive framer recognises DL/T 645 frame preambles.
- The web "add device" dialog accepts a meter address (`dlt645_addr`) and sends an
  internal add-meter message.
- The model names `DLT645-07` and `DLT645-97` exist as strings used in two UI list
  filters.
- **Missing:** nothing in the firmware acts on the add-meter request (it
  is silently ignored), nothing ever issues a DL/T 645 request on the bus, there is
  no request builder or data-id table, and no DL/T 645 model is in the built-in
  model lists.

So frames can be parsed but nothing can use them: an add-meter request is ignored
and the web call eventually times out (about 30 s), and a DL/T 645 reply on the bus
is treated as a failed Modbus transaction. The meters the dongle really supports on
RS-485 (DTSD1352, DTSU666, S100) are all Modbus RTU
devices and do not use this path.
