# Modbus TCP poll map: WiProxy (device type 34, product 1820)

Points the dongle polls from a WiProxy over Modbus TCP.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 11 of 12 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes |
|---|---|---|---|---|---|---|---|---|---|
| 8300 | 04 | 10 | str |  |  | dev_sn | Device S/N | 60 s |  |
| 8310 | 04 | 8 | str |  |  | dev_model | Device model | 60 s |  |
| 8318 | 04 | 15 | str |  |  | app_swversion | Application software version | 60 s |  |
| 8333 | 04 | 15 | str |  |  | build_swversion | Build software version | 60 s |  |
| 8348 | 04 | 15 | str |  |  | certified_swversion | Version | 60 s |  |
| 8363 | 04 | 1 | s16 |  |  | logger_slave_fault | Fault status | 60 s |  |
| 8365 | 04 | 10 | str |  |  | ip | ETH IP address | 60 s |  |
| 8375 | 04 | 1 | s16 |  |  | eth_conn_sts | Ethernet connection status | 60 s |  |
| 8376 | 04 | 16 | str |  |  | ap_ssid | WLAN SSID | 60 s |  |
| 8392 | 04 | 10 | str |  |  | wifi_ap_ip | WLAN hotspot IP address | 60 s |  |
| 8402 | 04 | 32 | str |  |  | wifi_ap_mac | WLAN client MAC address | 60 s |  |
| 8493 | 04 | 1 | u16 |  |  |  |  | 60 s |  |
