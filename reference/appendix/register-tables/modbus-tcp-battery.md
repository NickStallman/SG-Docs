# Modbus TCP map: battery (device type 44)

Registers the dongle serves over Modbus TCP for a battery stack.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 11 of 34 rows are named.

| address | fn | count | type | scale | unit | field | label | notes |
|---|---|---|---|---|---|---|---|---|
| 10701 | 04 | 4 | str |  |  |  |  |  |
| 10705 | 04 | 4 | str |  |  |  |  |  |
| 5448 | 04 | 25 | str |  |  |  |  |  |
| 10628 | 04 | 1 | s16 |  |  |  |  |  |
| 10629 | 04 | 1 | u16 |  |  |  |  |  |
| 10630 | 04 | 1 | u16 |  |  |  |  |  |
| 10631 | 04 | 1 | u16 |  |  |  |  |  |
| 10632 | 04 | 1 | u16 |  |  |  |  |  |
| 10633 | 04 | 1 | u16 |  |  |  |  |  |
| 10709 | 04 | 1 | u16 |  |  |  |  |  |
| 10710 | 04 | 1 | u16 |  |  |  |  |  |
| 10711 | 04 | 10 | str |  |  | dev_sn | Device S/N |  |
| 10721 | 04 | 15 | str |  |  | battery_version | Current version |  |
| 10736 | 04 | 2 | u32 |  |  |  |  |  |
| 10738 | 04 | 2 | u32 |  |  |  |  |  |
| 10740 | 04 | 1 | u16 |  |  | batt_operation_status | Battery operating status |  |
| 10741 | 04 | 1 | u16 | 0.1 | V | batt_voltage | Battery voltage |  |
| 10742 | 04 | 1 | s16 | 0.1 | A | batt_current | Battery current |  |
| 10743 | 04 | 1 | s16 | 0.1 | ℃ | batt_temp | Battery temperature |  |
| 10744 | 04 | 1 | u16 | 0.1 | % | batt_remain_power | Battery SOC |  |
| 10745 | 04 | 1 | u16 | 1 | % | batt_heathly | Battery SOH |  |
| 10746 | 04 | 2 | u32 | 0.1 | kWh | battdtot_charge_yeild | Total battery charge |  |
| 10748 | 04 | 2 | u32 | 0.1 | kWh | totbms_discharge_yeild | Total battery discharge (BMS) |  |
| 10750 | 04 | 1 | u16 |  |  |  |  |  |
| 10751 | 04 | 1 | u16 |  |  |  |  |  |
| 13066 | 04 | 2 | u32 |  |  |  |  |  |
| 13068 | 04 | 2 | u32 |  |  |  |  |  |
| 13070 | 04 | 2 | u32 |  |  |  |  |  |
| 13072 | 04 | 2 | u32 |  |  |  |  |  |
| 13074 | 04 | 2 | u32 |  |  |  |  |  |
| 13076 | 04 | 2 | u32 |  |  |  |  |  |
| 13078 | 04 | 2 | u32 |  |  |  |  |  |
| 13080 | 04 | 10 | str |  |  | dev_sn | Device S/N |  |
| 19939 | 04 | 2 | u32 |  |  |  |  |  |
