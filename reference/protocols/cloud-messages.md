# iSolarCloud uploads

This page lists every message the WiNet-S and WiNet-S2 publish to iSolarCloud on their own
initiative: its topic, when it is sent and what its `businessData` holds. Each payload is wrapped in
the envelope described in [cloud-envelope.md](cloud-envelope.md), on the topics described in
[cloud-mqtt.md](cloud-mqtt.md). Replies to commands are in [cloud-commands.md](cloud-commands.md).

> Modbus register numbers on this page are 1-based, as in Sungrow's published Modbus documents.

## How devices are described

### Elements

`businessData` is an array of **elements**. An element describes one device, and every element
starts with the same header. The keys appear in this order:

| Field | Type | Present | Content |
|---|---|---|---|
| `batchId` | string | `propertydata`, `devinfo` | `"999999999999999"` when unsolicited. When the message answers a command, it is the command's `batchId`. |
| `devCode` | number | always | The dongle's own id for the device (see below) |
| `devType` | number | always | The cloud device type (see the table below) |
| `upDevType` | number | cloud types 43 and 92 | Cloud type of the parent device |
| `upDevCode` | number | cloud types 43 and 92 | `devCode` of the parent device |
| `sn` | string | `devstatusdata` of types 1 and 14 | Device serial |
| `timestamp` | string | `devstatusdata` of types 1 and 14 | Local time with milliseconds, `YYYYMMDDhhmmssmmm` |
| `dataTime` | string | always | Local time of the snapshot, `YYYYMMDDhhmmss` |
| `data` | object | most families | `{"<pointId>":"<value>", …}` |

### devCode

**`devCode`** is not the Modbus address. The dongle numbers the devices it discovers in order:

- 1 is the first device it finds, then 2, 3 and so on.
- 247 is the dongle itself (the **logger**).

On a hybrid site (an SH10RS with two SBR192 stacks), the inverter is 1 and the stacks are 2 and 3,
although the stacks answer on Modbus addresses 200 and 201. The numbering survives reboots through
the dongle's saved device list. A factory reset or a different discovery order renumbers the
devices. At most 17 devices on the WiNet-S2 and 13 on the WiNet-S, logger included, are reported.

### Parent links

A battery stack (cloud type 43) or an Energy Bridge (92) is reported as a child of its inverter.
For a battery the two parent fields come from the battery's own registers, which the inverter
serves:

| Field | Source register | Value |
|---|---|---|
| `upDevCode` | 10750 (the inverter's Modbus address) | The `devCode` of the device at that address |
| `upDevType` | 10751 (the inverter's model class) | 1 when the register is below 3, 14 otherwise |

Both fields are always emitted for these types. If the source cannot be read, they are 0. A hybrid
site's stacks therefore appear as `"devCode":2,"devType":43,"upDevType":14,"upDevCode":1`. See
[../devices/sbr192/](../devices/sbr192/) for the battery itself.

### Device types

The dongle keeps its own device type for each device. It maps that type to the cloud `devType`
through a fixed table of 29 entries, and an unknown type maps to 0. A virtual "plant" device
(device type 3) exists inside the dongle but is never reported. Grid profiles and model families
are in [device-types-and-grid-profiles.md](../devices/device-types-and-grid-profiles.md).

| Dongle device type | Cloud `devType` | Role |
|---|---|---|
| 1 | 1 | String inverter |
| 4 | 1 | Inverter (other family) |
| 7 | 1 | Inverter (other family) |
| 21 | 1 | Residential string inverter, for example SG5.0RS |
| 24 | 1 | Inverter (other family) |
| 25 | 1 | Inverter (other family) |
| 2 | 22 | The dongle (logger) |
| 35 | 14 | Hybrid (storage) inverter, for example SH10RS |
| 44 | 43 | Battery stack, for example SBR192 |
| 5 | 7 | Meter |
| 6 | 7 | Meter |
| 18 | 7 | Meter (see [meters.md](../devices/meters.md)) |
| 46 | 51 | EV charger |
| 92 | 92 | Energy Bridge (see [ev-charger-and-energy-bridge.md](../devices/ev-charger-and-energy-bridge.md)) |
| 34 | 46 | Subordinate Sungrow logger |
| 8 | 4 | Combiner box |
| 9 | 21 | Plant equipment |
| 11 | 5 | Environment detector |
| 20 | 5 | Weather station |
| 12 | 19 | Plant equipment |
| 13 | 6 | Box transformer |
| 14 | 27 | Tracker |
| 16 | 10 | Plant equipment (cloud type 10 is also used for optimizer groups) |
| 29 | 10 | Plant equipment |
| 22 | 32 | Plant equipment |
| 27 | 26 | Plant equipment |
| 28 | 31 | Plant equipment |
| 30 | 23 | Plant equipment |
| 31 | 25 | Plant equipment |

Optimizers are not separate devices. They are reported inside their inverter's element as `childs`,
with cloud type 10 for an optimizer group and 41 for a single optimizer (see
[optimizers-and-mlpe.md](../devices/optimizers-and-mlpe.md)).

### Points and values

The keys of `data` are iSolarCloud **point ids**. Each point id stands for one value of one device
class. For each device class, the dongle holds a fixed table that assigns every point to one or more
categories, and the category decides which message carries it:

| Category | Message |
|---|---|
| Attribute | `rsp/propertydata` |
| Run data | `pub/rundata/5min` |
| Status | `pub/devstatusdata` |
| Fault record | `pub/faultmergedata` |
| Parameter-change event | `pub/faultdata` |
| General | `pub/generaldata` |
| Logger readback | `pub/devpointdata` |

Points per device class on the WiNet-S2 (the WiNet-S tables are smaller for several classes):

| Class (cloud type) | Attribute | Run | Status | Fault | Param. event | General |
|---|---|---|---|---|---|---|
| Logger (22) | 20 | 10 | 1 | – | – | – |
| String inverter (1) | 82 | 210 | 11 | 11 | – | – |
| Hybrid inverter (14) | 127 | 207 | 12 | 1 | 131 | 9 |
| Meter (7) | 1 | 31 | – | – | – | – |
| Battery stack (43) | 36 | 71 | 2 | 1 | – | – |
| EV charger (51) | 14 | 11 | 10 | 1 | – | – |
| Energy Bridge (92) | 8 | 40 | – | – | – | – |
| Subordinate logger (46) | 6 | 12 | – | – | – | – |

**Value rules:**

- **Format.** Every value is a JSON **string**. Integers are sent raw in decimal. A scaled value is
  the raw register value multiplied by the point's scale, printed with up to 10 significant digits
  (16-bit sources) or 13 (32-bit sources). For most scaled points the raw value passes through
  single precision, so 32-bit values above 2^24 lose their low digits; points of the
  double-precision format do not. Text is sent up to its first NUL, and binary blocks
  as lowercase hex.
- **Scale.** The scale is the cloud's own and differs from the register's display scale. For example:
  - A string inverter's daily yield (point `1`) is the 0.1 kWh register × 100.
  - A battery state of charge is sent as a fraction: a hybrid's battery level (point `13141`) of 97.5 %
    is sent as `"0.975"`.
- **Invalid values.** A point whose register holds the invalid marker for its type is left out:
  `0x7FFF`, `0xFFFF`, `0x7FFFFFFF`, `0xFFFFFFFF`, all-`0xFF`, or an all-NUL string.
- **Empty elements.** An element whose points are all left out is still sent, with `"data":{}`. The
  one exception is `generaldata`, which is not sent at all.
- **Offline devices.** A device that is not communicating is skipped by the per-device messages.

Some frequently used points:

| Point | Class | Value |
|---|---|---|
| 24 | String inverter | Active power, W |
| 1 / 2 | String inverter | Daily / total yield |
| 13011 | Hybrid | Active power, W |
| 13003 | Hybrid | Total DC power, W |
| 13141 | Hybrid | Battery level (fraction) |
| 13126 / 13150 | Hybrid | Battery charging / discharging power |
| 58601 / 58602 | Battery stack | Stack voltage, V / stack current, A |
| 58604 / 58605 | Battery stack | State of charge / state of health (fractions) |

## Cadence summary

The dongle runs a 1 s scheduler. Periods are counted in wall-clock seconds.

**Gates.** Scheduled messages are not sent:

- while the clock is invalid;
- during the configured silence window;
- while the session is down.

The CONNACK burst ([cloud-mqtt.md](cloud-mqtt.md#on-every-connack)) ignores the silence window and
the post-connect hold.

| Topic | When |
|---|---|
| `pub/rundata/5min` | First connect (after a 30 s hold), then every period (default 5 min) after a random delay |
| `rsp/propertydata` | Every CONNACK; 180 s after the first connect; logger every 24 h; on attribute change |
| `pub/devinfo` | After connecting (see the section) and at server-switch confirm |
| `pub/beatdata` | Every CONNACK; every 1800 s; 180 s after a device goes online or offline |
| `pub/willdata` | Published by the broker when the session drops without DISCONNECT |
| `pub/devstatusdata` | On change; every 7200 s; every reconnect |
| `pub/generaldata` | Every CONNACK (one device); on change |
| `pub/devpointdata` | On change; every 24 h; every reconnect |
| `pub/faultmergedata` | On change of a device's fault record; every 3600 s |
| `pub/faultdata` | On parameter change, from 180 s after the first connect |
| `pub/faultdata/history` | At link-up and every reconnect, while stored faults remain |
| `pub/rundata/history` | Every 30 s while a backfill marker is set |
| `pub/rundata/second` | During a live-data session, every 10 s |
| `pub/fcas/second` | Every `uploadCycle` s while an FCAS capture runs (after an inverter trigger) |

## pub/rundata/5min

**Purpose:** periodic telemetry. The cloud's charts and energy totals come from this message.

**Cadence:**

- **Period.** A logger setting in minutes, default 5. The cloud can change it with a
  logger-parameter command ([cloud-commands.md](cloud-commands.md)). A value of 0 means 5, and the
  period is forced to 5 for the first 30 minutes after boot.
- **Trigger.** When the local minute is a multiple of the period, the dongle takes the boundary time,
  with seconds set to 0, as `dataTime`. It then waits a random delay of 60 to 179 s, drawn once per
  boot, and sends.
  - A period that does not divide 60 restarts at every full hour.
  - While a live-data session is sending, rundata waits for it.
- **First connect.** 30 s after the first CONNACK of a boot, one round is sent with `dataTime` set to
  the current time.

**One device per message.** The dongle walks its device list in slot order, logger included, and
skips offline devices. Each message holds one element. The next device's message goes out when the
previous one is acknowledged, or when its wait fails. A failure does not stop the round.

```json
[{"devCode":1,"devType":14,"dataTime":"20261024143000",
  "data":{"13001":"312.4","13011":"2410","13003":"2650","13141":"0.655","13150":"240"}}]
```

A battery element carries its parent link (`"devCode":2,"devType":43,"upDevType":14,"upDevCode":1`).
The logger element is described in [The logger element](#the-logger-element).

## pub/rundata/history

**Purpose:** backfill after an outage.

**Storage.** The dongle records timestamped per-device snapshots in files on its external-flash
filesystem, under `/usrdataout/histroy` (sic):

- Each file covers one device and one time bucket. File names have the form
  `<start>_<end>_HIS_<device>_<n>`.
- Optimizer data is stored in parallel, as `…_MLPE_HIS_…` files, with layout snapshots alongside.
- The store has a fixed capacity. The oldest file is deleted when it is full.
- The same store serves time-range reads from the local app.

**Marker.** When a `rundata/5min` publish fails, because the PUBACK never arrives or the publish
errors, the dongle saves a persistent marker if none is set yet. The marker is the last 5-minute
boundary minus 1 s.

**Replay.** Every 30 s after the first connect, if the marker is set and later than 2000-01-01:

1. The dongle queues every online device except the logger.
2. It reads stored records for the window from the marker to the earlier of now and marker + 600 s.
3. It publishes them on this topic.
4. When nothing is left to send, it clears the marker.

**What is replayed.** The replay builds a message only for an inverter that has optimizers. The
element shown below is published only when that inverter's optimizer-status value is non-zero, and
it carries a `childs` list with one hex `dataBit` block per optimizer:

```json
[{"devCode":1,"devType":1,"dealType":41,"offlineStartTime":"…","offlineEndTime":"…","childs":["…"]}]
```

Ordinary inverter, meter and battery run data lost during an outage is **never re-sent**. The cloud
shows a gap for that period.

## pub/rundata/second

**Purpose:** live data. The cloud opens a live-data session with command 10421 on
`cmd/<serial>/second/…`. The command lists the point ids it wants, up to 400 per device.

**Cadence:** at every local 10-second boundary while the session runs. For inverters with optimizers,
optimizer data is added at a period set by logger point 23259 (default 10 s; WiNet-S2 only).

**Element:** the same shape as `rundata/5min`, with only the requested points. Values are collected from the
dongle's device data at send time.

The cloud offers live data only for devices whose `functionEnum` declares it (see
[rsp/propertydata](#rsppropertydata)).

## pub/beatdata

**Purpose:** online state. The portal shows a device as online from this message, which overrides
the retained will.

**Cadence:**

- every CONNACK, before `propertydata`;
- every 1800 s;
- 180 s after any device goes online or offline, or after an optimizer status change.

The periodic and change-driven beats respect the silence window. The CONNACK beat does not.

**Elements:** one per device slot, in slot order, with the logger included. A device whose
communication status cannot be read is left out.

| Field | Type | Content |
|---|---|---|
| `devCode` | number | Device id |
| `devType` | number | Cloud type |
| `beat` | number | 1 = communicating, 0 = not communicating. The logger always reports 1. |
| `childs` | array | Inverters with optimizers only: `[{devCode, devType:10, beat, childs:[{devCode, devType:41, beat}]}]` |

```json
[{"devCode":247,"devType":22,"beat":1},{"devCode":1,"devType":14,"beat":1},
 {"devCode":2,"devType":43,"beat":1},{"devCode":3,"devType":43,"beat":1}]
```

## pub/willdata

This is the MQTT will. The dongle registers it in CONNECT with QoS 1 and retain set, and the broker
publishes it if the session drops without a DISCONNECT.

- **`businessData`:** `[{"devCode":247,"devType":22,"beat":0}]`
- **Envelope:** `packgeCount` and `packgeId` are both `"1"`, and `sendTime` is the time of the
  CONNECT.

## pub/devinfo

**Purpose:** the dongle's link details. This is the only message that reports the broker settings.

**Cadence:**

| Dongle | When it is sent |
|---|---|
| WiNet-S2 | The tick after the first-connect round, every reconnect, and a server-switch confirm |
| WiNet-S | Every CONNACK and a server-switch confirm |

**Element:** logger only, with `batchId` `"999999999999999"`.

| Point | Type | Content |
|---|---|---|
| `10461` | object of numbers | `{"10479":2,"10480":1,"10481":1}`, a constant. Probably a capability block. |
| `38338` | string | Current broker as `<host>;<port>;<key>` |
| `38337` | string | Backup ("old") broker in the same form. It equals 38338 except between a switch and its confirm. |
| `38781` | string | WiNet-S2: always `"0000000000000000"`. WiNet-S: the [link-failure mask](cloud-mqtt.md#link-failure-reason-bits) as 16 hex characters, in little-endian byte order. |
| `5767` | string | IP address of the active network link |
| `5768` | string | A 32-character bit string, constant `"1111111"` followed by 25 zeros. Probably a list of supported services. |

The broker key is sent in clear inside the TLS session.

```json
[{"batchId":"999999999999999","devCode":247,"devType":22,"dataTime":"20261024143031",
  "data":{"10461":{"10479":2,"10480":1,"10481":1},
          "38338":"<host>;16668;<key>","38337":"<host>;16668;<key>",
          "38781":"0000000000000000","5767":"<link IP>",
          "5768":"11111110000000000000000000000000"}}]
```

## pub/devstatusdata

**Purpose:** device status.

**Cadence:**

- when a status point changes, checked every second after the first connect;
- every device every 7200 s;
- every device at each reconnect.

Only online devices are sent, one element each.

**Element:** the standard header. Cloud types 1 and 14 also carry `sn` and `timestamp`. `data` holds
the status points:

| Class | Points |
|---|---|
| Logger | 23017 (probably the logger state) |
| String inverter | 29 (operating status), 7599 to 7602, 7965 to 7967, 5334, 7969, 65053 |
| Hybrid | 13146 (operating status), 13369, 13702, 18120 to 18123, 18127 to 18129, 18133, 86157 |
| Battery stack | 100231, 100232 (WiNet-S2 only) |
| EV charger | 33708 to 33745 |

```json
[{"devCode":1,"devType":14,"sn":"A2xxxxxxxxx","timestamp":"20261024143005123",
  "dataTime":"20261024143005","data":{"13146":"0","18120":"…"}}]
```

## pub/generaldata

**Purpose:** battery identity on hybrid inverters. The cloud probably uses it to notice an added
battery.

**Cadence:**

- at every CONNACK, for the **first** online device that has general points;
- when one of those points changes.

It is not sent periodically.

**Element:** the standard header, then `data`, then the field `businessType`, which is always
`"addBattery"`. The points are hybrid only: 18070 to 18074 and 38667, 38669, 38671, 38673. An element
with empty `data` is not sent.

```json
[{"devCode":1,"devType":14,"dataTime":"20261024143001","data":{"18070":"…","18071":"…"},"businessType":"addBattery"}]
```

The same topic also carries "monitor data" (`businessType` `"upFrequency"`) after cloud command 10375.

## pub/devpointdata

**Purpose:** readback of logger settings that the cloud can write.

**Cadence:**

- on change;
- every 24 h, counted from boot;
- at every reconnect.

**Element:** the logger only, with four points:

| Point | Setting | Default |
|---|---|---|
| 10339 | Hotspot (access point) enabled | 1 |
| 17075 | Remote R&D maintenance switch | 0 |
| 64729 | A logger setting, also writable as point 10482 (meaning unknown) | 0 |
| 64881 | Modbus TCP server on port 502 enabled | 1 |

## pub/faultmergedata

**Purpose:** device fault and alarm state.

**Cadence:**

- when a device's fault record changes;
- every online device with a fault record every 3600 s.

The builder checks the clock and the silence window itself.

**Element:** the standard header, with `dataTime` set to the current time. The `data` object is
removed and replaced by top-level fields:

| Field | Type | Content |
|---|---|---|
| `faultBit` | string | Lowercase hex of the binary record below |
| `LocationCode` | object | String inverters only, when location-coded faults are present: `{"<code>":"<metres, one decimal>"}` |

The binary record (61 bytes for an inverter or battery, so `faultBit` is 122 hex characters):

| Offset | Size | Content |
|---|---|---|
| 0 | 1 | Cloud `devType` (low byte) |
| 1 | 1 | `devCode` (low byte) |
| 2 | 7 | Fault time, local: year (u16 big-endian), month, day, hour, minute, second |
| 9 | 2 | Width code, u16 big-endian: `0x0004` for the fault block |
| 11 | 50 | The device's fault block, input registers 5448 to 5472 as big-endian words |
| 61 | 2 | Logger only: u16 0 |

The fault block holds the following words:

| Register | Content |
|---|---|
| 5448 to 5451 | Flags. Bit `0x2000` of the first word means a code list follows. |
| 5452 | Number of active codes, at most 10 |
| 5453 to 5472 | Up to ten pairs of (fault code, class) |

**LocationCode.** For string inverters, codes 1716 to 1725 with class 14 have a location value read
from a matching inverter register. It is sent as value × 0.1, probably the arc-fault distance in
metres.

The fault codes themselves are listed in [error-codes.md](../appendix/error-codes.md). For an
inverter with optimizers, an hourly variant nests per-optimizer 2-byte `faultBit` values under
`childs`.

```json
[{"devCode":1,"devType":14,"dataTime":"20261024150000","faultBit":"0e0107ea0a180e1e000004…"}]
```

## pub/faultdata

Despite its name, this topic carries **parameter-change events**, not faults.

**Source.** On a hybrid inverter, 131 settings (points 13323 to 13722) are watched. A change of any
of them queues an event.

**Cadence:**

- Events wait until 180 s after the link first comes up, then drain.
- Each message takes up to 50 pending events of the same device class.
- The next batch follows the PUBACK of the previous one.

**Element:** one per event, holding a single point:

```json
[{"devCode":1,"devType":14,"dataTime":"20261024151203","data":{"13624":"1"}},
 {"devCode":1,"devType":14,"dataTime":"20261024151203","data":{"13527":"50"}}]
```

## pub/faultdata/history

**Purpose:** replay of faults stored in the dongle's fault log while the link was down.

**Cadence:**

- At the first link-up of a boot with any device online, the dongle requests stored records from its
  fault log, up to 4 at a time.
- It repeats the request while more records remain and the count has stopped changing for 5 s.
- The same happens at every reconnect.

**Element:** one device per message.

| Field | Type | Content |
|---|---|---|
| `dataTime` | string | Now |
| `devCode`, `devType` | number | Device |
| `batchNum` | number | Batch number of this replay |
| `faultNum` | number | Records for this device in this batch |
| `faultList` | array | One object per record: `faultTime` (string, `YYYYMMDDhhmmss`), `faultValue`, `faultLevel`, `faultId`, `faultReason` (numbers) |

`dataTime` comes first in this element, before `devCode`.

## pub/fcas/second

**Purpose:** FCAS fast recording (frequency-control ancillary services). The cloud starts a recording
task with command 10369. The dongle samples every `saveCycle` ms (a 200 ms timer, so at most
5 Hz), keeps a 20 s pre-trigger buffer and starts capturing when the inverter reports the event. It
publishes the recording on this topic. Task setup and fields are in
[diagnostics-and-log-export.md](diagnostics-and-log-export.md#fcas-fast-collection-10369).

## rsp/propertydata

**Purpose:** device identity and capabilities. This is the only per-device identity message, because
`devinfo` covers the logger only. It is published on the `rsp` branch with `cmdType` `"10310"`.

**Cadence:**

- every CONNACK, all online devices back to back;
- 180 s after the first connect;
- the logger every 24 h;
- a device again after one of its attributes changes;
- on request by command 10310, with the command's `batchId`.

**Element:** the keys appear in this order:

| Field | Type | Content |
|---|---|---|
| `batchId` | string | `"999999999999999"`, or the command's id |
| `devCode`, `devType`, `upDevType`, `upDevCode`, `dataTime` | | Standard header |
| `data.continuityPoint` | array of strings | Point ids of multi-part values (see below). Left out when none applies. |
| `data."<pointId>"` | string | Attribute points |
| `functionEnum` | string | 16-byte capability bitmap as 32 lowercase hex characters, byte 0 first |
| `cmdType` | string | `"10310"` |
| `devName` | string | Device name, for example `"WiNet-S2"` |
| `sn` | string | Device serial |
| `devModel` | number | Device model code, for example 1824 for the WiNet-S2, `0x0D1B` (3355) for the SH10RS, `0x2606` (9734) for the SG5.0RS, `0x20EA` (8426) for an SBR192 stack |
| `devVersion` | string | Firmware version |
| `result` | number | 1 |

**`continuityPoint`** lists the attribute points that hold long values:

| Class | Points |
|---|---|
| Hybrid | 13400 (the inverter's capability bitmap), 13747, 13779, 18300 |
| String inverter | 7783 (capability bitmap), 7651, 7852, 7884, 5346 |
| Battery stack | 58804, 58808 |
| Meter | None |

### functionEnum

`functionEnum` tells the cloud what the device can do. Bit *n* is in byte *n* / 8, with mask
1 << (*n* mod 8). The dongle does not compute it: it sends a constant fixed in the firmware.

| Dongle | Value sent for the logger and every inverter, battery, charger and Energy Bridge |
|---|---|
| WiNet-S2 | `07100000d7f1c427b065200000100600` |
| WiNet-S | `07000000d7f144210041000000000600` |

Exceptions to the constant:

- **Battery stacks.** Bit 108 (byte 13, mask `0x10`) follows bit 4 of the first word of the stack's
  8-byte status block (register 10705).
  It is cleared if that register cannot be read. The WiNet-S does not adjust this bit.
- **Meters.** A meter has no bitmap of its own. The WiNet-S2 sends all zeros for it. The WiNet-S sends
  no `propertydata` for a meter at all.
- **Subordinate loggers** (cloud type 46) get the logger's bitmap.

**What it gates.** The cloud uses the bitmap to decide which features to offer for a device. A
device that reports a truncated or zeroed bitmap is never offered Live data, and the cloud never sends
it command 10421. With the full constant, the cloud opens live data within about a minute. Bit 45 is
the one the app reads as "live-data capable". The meaning of the other bits is unknown.

The example below is a battery stack on a hybrid site, with a placeholder serial. An inverter
element has the same shape without the parent link.

```json
[{"batchId":"999999999999999","devCode":2,"devType":43,"upDevType":14,"upDevCode":1,
  "dataTime":"20261024143212",
  "data":{"continuityPoint":["58804","58808"],"58804":"…","58808":"…"},
  "functionEnum":"07100000d7f1c427b065200000100600","cmdType":"10310",
  "devName":"SBR192","sn":"A2xxxxxxxxx","devModel":8426,"devVersion":"…","result":1}]
```

## The logger element

The dongle reports itself as **devCode 247, devType 22** in `rundata`, `propertydata`, `beatdata`,
`devinfo`, `devstatusdata` and `devpointdata`.

Attribute points (`propertydata`):

| Point | Content | Typical value |
|---|---|---|
| `-1` | Product type. The WiNet-S2 sends this key literally as `"-1"`; the WiNet-S omits it. | `1824` |
| 23207 | Firmware version | `WINET-SV300.001.03.P032`, `WINET-SV200.001.00.P046` |
| 23208 | Secondary software version | |
| 23210 | Attribute upload period, s | 86400 |
| 23211 | Probably a protocol or hardware id | 152 |
| 23212 | Probably a hardware platform code | 1580 (S2), 1068 (S) |
| 23241 | IP address of the active link | |
| 23242 | Wi-Fi network name (SSID) | |
| 23246 | Boot loader and flash size | `1-WINET-SV300.001.03.B001;2-32MB` |
| 23247 | Module version (WiNet-S2 only) | |
| 23248, 23249 | Date code and build date | |
| 23259 | Live-data period, s (WiNet-S2 only) | |
| 23272 | Never written | `0` |
| 23274 | A logger setting (also point 10482) | 0 |
| 23275 | Modbus TCP server enabled | 1 |
| 23281 | Unknown | 0 |
| 23284, 1246 | Remote-control mask (two copies) | 1279 (S2), 1263 (S) |
| 29807 | Time source (3 = iSolarCloud, the default; see [rs485-master.md](rs485-master.md#clock-synchronisation)) | 3 |

Run points (`rundata/5min`):

| Point | Content |
|---|---|
| 23001, 23008, 23015, 23209 | Cellular signal, SIM ICCID, network details, operator. These are not used on WiNet hardware: the strings are dropped and 23001 reads 0. On the WiNet-S the ICCID is point 23004. |
| 23012 / 23013 | RS-485 frames sent / dropped |
| 23014 | Wi-Fi signal strength, dBm |
| 23016 | Ethernet link state: 1 = cable linked, 0 = down; meaning inferred, not confirmed |
| 10037 | Number of devices under the logger (meaning inferred, not confirmed); communication devices (dongle device type 23) are not counted |
| 10038 | Number of devices with an active fault (meaning inferred, not confirmed). Only devices of dongle device type 1 (string-inverter family) and EV chargers are counted; hybrids and batteries never are. |
