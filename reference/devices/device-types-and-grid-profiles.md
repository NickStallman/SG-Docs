# Device types, settings templates and grid profiles

Every device a WiNet dongle knows about is a node in its data model with a **device id**, a **device code** (the
model number the device reports) and a **device type** (the dongle's class for it). For inverters the dongle also
derives a **protocol type**, which picks the poll table, and an **inverter type**, which together with the protocol
type picks the per-region settings template used for grid-code setup. This page describes those classifications as
both dongles implement them, the settings-template families, and the grid profiles they offer.

Register numbers are 1-based, as in Sungrow's Modbus documentation; the address in the frame is one less.

## 1. Device types

| Type | Role | How a node of this type comes to exist |
|---|---|---|
| 1 | String inverter | Search, manual add, or a device code outside the ranges in section 2 |
| 21 | Residential ("home") inverter | Device code in a residential range |
| 35 | Hybrid ("storage") inverter | Device code `0x0D00`–`0x0EFF` |
| 2 | The dongle itself (logger) | Always present: device id 247, device code `0x720` (WiNet-S2) or `0x715` (WiNet-S) |
| 3 | Virtual plant device | Always present: device id 0, device code `0xFFFF` |
| 18 | Energy meter ("ammeter") | Added by an operator ([meters](meters.md)) |
| 34 | Sungrow logger family: WiMeter, WiProxy | Added through the add-device call ([wimeter-and-wiproxy.md](wimeter-and-wiproxy.md)) |
| 44 | Battery stack (SBR) | Child of a hybrid inverter: reported in inputs 10451–10458 and probed |
| 46 | EV charger | Child of a hybrid inverter (input 5621 bit 0), or manual add ([ev-charger-and-energy-bridge.md](ev-charger-and-energy-bridge.md)) |
| 92 | Energy Bridge | Child of a hybrid inverter (input 5621 bit 2) |
| 93 | Internal virtual modules | Created by the dongle at boot |
| 8, 9, 11, 13, 14, 15, 16 | Template-defined: combiner box (8), class 9, environment detector (11), box transformer (13), tracker (14), PID module (15), class 16 | Only from a point-table file; the dongle has no data model for them, so the add fails |
| 20 | Weather station | Stub: "no support" |
| 23 | Communication device | Stub: "no support" |
| 24 | Central inverter | Stub: "no support" |
| any, with communication protocol IEC-104 | IEC-104 device | Stub: the client link is created and connected, then the module reports "no support" |

The data model holds at most 18 device nodes. The dongle allocates device ids from 1 upwards (from 2 with a regional
flag set); id 247 is its own.

Cloud device types the dongle reports for these classes: 1 and 21 → 1, 35 → 14, 2 → 22, 18 → 7, 34 → 46, 44 → 43,
46 → 51, 92 → 92 (29 entries in all, identical on both dongles).

## 2. Device code to device type

The dongle maps an inverter's device code (input 2515) to a type:

| Device code | Type |
|---|---|
| `0x0110`–`0x0115`, `0x011C`–`0x0127` | 21 |
| `0x0133`, `0x013C`, `0x013E`, `0x013F`, `0x0142`, `0x0143`, `0x0147`–`0x0149` | 21 |
| `0x2400`–`0x27FF` (includes the SG5.0RS, `0x2606`) | 21 |
| `0x0D00`–`0x0EFF` (includes the SH10RS, `0x0D1B`) | 35 |
| Any other code, manual add, low byte of 2535 = `R` | 21 |
| Any other code, manual add, low byte of 2535 = `B` | 35 |
| Any other code otherwise | 1 |

The `R` / `B` fallback works only on the manual add-by-type path; a device found by search with an unlisted code is
always type 1. Inverter discovery itself is on [../protocols/device-discovery.md](../protocols/device-discovery.md).

## 3. Protocol type

After an inverter exists, the dongle reads input 2515–2580 (66 registers) and derives the protocol type from the
device code, the four-character protocol code at 2534–2535 and the two-character tag at 2578:

1. Tag `ZS` → **3**, the incremental protocol (function `0x65` change stream over the private map).
2. Otherwise, protocol code containing `AA` and sorting at or above `AA10` → **2**, the "AA10+" map with the capability
   bitmap at 5520.
3. Otherwise by device code: `0x0D03`, `0x0D06`, `0x0D07`, `0x0D09`, `0x0D0C` → **5**, the storage map; `0x0027`,
   `0x0074`, `0x010F`, `0x0116`, `0x011B`, several `0x013x`/`0x014x` codes, `0x0E00`–`0x0E03`, `0x1061`, `0x1062`,
   `0x2FC0`–`0x2FFF` → **4**, an older-LCD variant of the classic map; anything else → **1**, the classic
   residential/string map.

An exception reply to the probe gives protocol 1.

| Protocol | Poll table rows (WiNet-S2 / WiNet-S) | Polled by the string round (types 1, 21) | Polled by the storage round (type 35) |
|---|---|---|---|
| 1, 4 | 167 / 166 | yes | **no** |
| 2 | 623 / 570 | yes | yes |
| 3 | 636 / 588 | yes (incremental) | yes (incremental) |
| 5 | 133 / 132 | **no** | yes |

So a type 35 device with protocol 1 or 4, or a type 1 / 21 device with protocol 5, is never table-polled. Only
protocols 2 and 3 honour the capability bitmap. The WiNet-S2 tables for protocols 2 and 3 carry 18 extra rows for the
optimizer window ([optimizers](optimizers-and-mlpe.md)).

For a `ZS` device the WiNet-S uses protocol 3 internally but **publishes** the value step 2 or 3 would give (or 0 when
the code lacks `AA`). The SH10RS and SG5.0RS both report `AA10` and `ZS`, so both dongles end on the same template family
for them.

## 4. The add-device list

The web UI's add-device list comes from the dongle and has six entries:

| Entry | Type |
|---|---|
| String inverter | 1 |
| Solar inverter | 21 |
| Storage inverter | 35 |
| Ammeter | 18 |
| Sungrow logger | 34 |
| Charging pile | 46 |

For types 1, 21 and 35 with no device code the UI opens the add-by-type scan (an address range probed with function 4
at 2505 × 33); otherwise it posts the add-device call. The address range is 1–246 for inverters and 1–255 otherwise.
The WiNet-S2 web UI drops the charging-pile entry on its "WiFi-P2" product variant. The WiNet-S2 UI also adds optimizer
tabs to the inverter pages for installer roles, filtered by whether the inverter reports optimizers; the WiNet-S UI has
none.

## 5. Settings-template family

Grid-code setup uses one of four per-region settings templates. The dongle picks the family from **(inverter type,
protocol type)**; the inverter type is holding register 30644, read only by the protocol 1/4 poll table, so for every
other protocol it stays 0.

| Inverter type (30644) | Protocol | Family | Country register | Grid-code register | Power switch |
|---|---|---|---|---|---|
| 0 | 1 | `str_pv` | 30500 | 30502 | 5006 |
| 1 | 1 | `res_new_pv` | 30643 | — | 5006 |
| 0 | 5 | `res_es` | 30643 | — | 13000 |
| 0 | 4 | none ("not supported") | 30643 | 30644 | 30045 |
| 0 or 1 | 2 or 3 | `unit` | 32310 | 32311 | 5006 |
| any other pair | | `unit` template named, but no register descriptor: the setup is refused | — | — | — |

The `unit` family also writes the grid standard at 30036 when bit 5 of capability word 32 is set, and has a grid type
at 30037. For the protocol 4 entry, 30644 doubles as the grid-code register, so a non-zero grid code moves the device
off that entry. The SH10RS and SG5.0RS are both protocol 3 and therefore `unit`, with country 6.

### 5.1 Parameter pages and installer catalogue

The dongle's own parameter pages come from compiled tables, one set per family, with rows per page type:

| Page type | Page |
|---|---|
| 0 | Setup picker (country, grid code) |
| 1 | Run parameters |
| 2 | Protection parameters |
| 3 / 6 | System parameters (power switch, factory reset) |
| 4 | Arc-fault device check (not a parameter page) |
| 7 | Power regulation |
| 8 | Other protection |
| 9 | Energy management |
| 17 | Overview data (no web page of its own) |
| 18 | Optimizer |

| Family | Rows per page type |
|---|---|
| `str_pv` | 0: 2, 2: 50, 3: 2, 7: 79, 17: 4, 18: 3 |
| `res_new_pv` | 0: 1, 2: 21, 3: 2, 4: 5, 7: 73, 17: 4, 18: 3 |
| `res_es` | 0: 1, 2: 21, 3: 1, 7: 61, 9: 54, 17: 4, 18: 3 |
| `unit` | 0: 2, 1: 141, 2: 45, 3: 2 + 2, 4: 5, 7: 92, 8: 84, 9: 64, 17: 4, 18: 3 |
| none (0, 4) | 0: 2, 2: 50 (the `str_pv` table), 3: 2, 17: 4, 18: 3 |

That is 899 rows in all: 613 with a range, 325 with a cross-row ordering rule, 491 gated by a capability bit. Each row
holds a register, a data type and scale, a static range (about 200 rows compute their live range from the inverter's
rated voltage, frequency and version), an ordering rule such as "stage-2 undervoltage ≤ stage-1 undervoltage", a
visibility relation and a role mask. The ordering rules are enforced only by the web UI, and eight of them are
malformed and silently ignored; the dongle firmware enforces none.

The settings templates themselves describe the writable installer items. Across the four families they hold 312
settings: 71 `str_pv`, 69 `res_es`, 50 `res_new_pv`, 122 `unit` (252 numeric, 37 switches, 23 lists).

## 6. Grid profiles

Each template's grid-protection section lists "power company" options; options that share a value are variants of one
option. The templates in force offer, for Australia (country 6), **7 options with 10 variants**:

| Family | Option | Standard | Variants |
|---|---|---|---|
| `str_pv` | 1 | AS/NZS 4777.2:2015 | one: 20 register runs |
| `res_es` | 1 | AS/NZS 4777.2:2015 | one: 16 runs |
| `res_new_pv` | 1 | AS/NZS 4777.2:2015 | one: 14 runs |
| `unit` | 12 | AS/NZS 4777.2:2015 | one: 19 runs |
| `unit` | 13 | AS/NZS 4777.2:2020 Australia A | PV (5479 = 1 or 2): 39 runs, 111 registers; energy storage (5479 = 3–6): 41 runs, 121 registers |
| `unit` | 14 | AS/NZS 4777.2:2020 Australia B | as option 13 |
| `unit` | 15 | AS/NZS 4777.2:2020 Australia C | as option 13 |

A profile write covers, in this order:

1. the family's country register, set to the country code (6);
2. for `unit` when capability word 32 bit 5 is set, 30036 set to the option code;
3. the chosen variant's register runs, in template order, with the template's values sent verbatim (32-bit values low
   word first; all-`F` values skipped).

The variant of a 2020 option is chosen by reading input 5479 first. All protection writes use function `0x10`;
frames are not sorted, and the web UI places the country, grid-code and standard registers first. The inverter is then
started by writing `0xCF` to the family's power-switch register (the power switch on the system-parameters page). The grid type (30037) is not
part of any profile. The same templates are what the cloud's template download returns; the regional and legacy
details are on [../protocols/regional-and-legacy-features.md](../protocols/regional-and-legacy-features.md) and the
inverter side on [sh10rs/grid-codes-and-drm.md](sh10rs/grid-codes-and-drm.md).

## 7. Behaviours worth knowing

- **Batteries are listed all or nothing.** The dongle probes every battery address the hybrid reports (inputs
  10451–10458 on the WiNet-S2, 10451–10455 on the WiNet-S) with function 4, 10710 × 11, but adds them only if **every**
  probed stack answered. One silent stack keeps all of them out of the device list.
- **Only one SBR model is named.** The dongle names device code `0x20E6` "SBR064"; every other battery code shows as
  "SBR000" until polling fills in the model ([SBR192 pages](sbr192/)).
- **Children are probed but not polled.** EV chargers and Energy Bridges are added but never polled cyclically
  ([ev-charger-and-energy-bridge.md](ev-charger-and-energy-bridge.md#4-after-the-device-exists-never-polled)).
- **Type 35 with protocol 1 or 4 is never polled**, nor type 1 / 21 with protocol 5 (section 3).
- **The protocol type the WiNet-S publishes for a `ZS` device** differs from the one it uses (section 3).

## Not yet known

- The meaning and default of holding register 30644 on models that serve it (0 is named "mid-power" in the dongle's
  logs, 1 "residential new PV").
- Whether a device-code change during operation re-runs the classification.
- Which of 30500, 30643 and 30646 a type 35 device with protocol 1 or 4 actually implements.
- Whether writing the country register makes the inverter reload default protection values (the profile order writes
  the country first, so a reload would be overwritten by the following runs).
