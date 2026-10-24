# RS-485 poll map: EV charger (device type 46)

Registers defined for an EV charger reached through the inverter. The dongle reads the identity block only.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 25 of 62 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes |
|---|---|---|---|---|---|---|---|---|---|
| 21201 | 04 | 10 | str |  |  | dev_sn | Device S/N | 1 h |  |
| 21215 | 04 | 1 | u16 |  |  |  |  | 1 h |  |
| 21216 | 04 | 8 | str |  |  | dev_model | Device model | 1 h |  |
| 21224 | 04 | 1 | u16 |  |  | dev_code | dev_code | 1 h |  |
| 21225 | 04 | 1 | u16 |  |  | phase | Number of phases | 1 h |  |
| 21226 | 04 | 15 | str |  |  | soft_version | Software version | 1 h |  |
| 21241 | 04 | 3 | str |  |  | pcb_version | PCB version | 1 h |  |
| 21244 | 04 | 15 | str |  |  | lcdsoft_version | Light board software version | 1 h |  |
| 21259 | 04 | 3 | str |  |  | lcdpcb_version | Light board PCB version | 1 h |  |
| 21262 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21263 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21264 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21268 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21269 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21270 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21271 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21272 | 04 | 1 | u16 |  | W | min_power | Min. charging power | 2 s |  |
| 21273 | 04 | 2 | u32 |  | W | max_power | Max. charging power | 2 s |  |
| 21275 | 04 | 25 | str |  |  |  |  | 2 s |  |
| 21300 | 04 | 2 | u32 |  | Wh | tot_energy_a | Total delivered energy | 2 s |  |
| 21302 | 04 | 1 | u16 | 0.1 | V | charging_volt_r_a | Phase A charging voltage | 2 s |  |
| 21303 | 04 | 1 | u16 | 0.1 | A | charging_curr_r_a | Phase A charging current | 2 s |  |
| 21304 | 04 | 1 | u16 | 0.1 | V | charging_volt_s_a | Phase B charging voltage | 2 s |  |
| 21305 | 04 | 1 | u16 | 0.1 | A | charging_curr_s_a | Phase B charging current | 2 s |  |
| 21306 | 04 | 1 | u16 | 0.1 | V | charging_volt_t_a | Phase C charging voltage | 2 s |  |
| 21307 | 04 | 1 | u16 | 0.1 | A | charging_curr_t_a | Phase C charging current | 2 s |  |
| 21308 | 04 | 2 | u32 |  | W | charging_power_a | Charging power | 2 s |  |
| 21310 | 04 | 2 | u32 |  | Wh | charging_energy_a | Energy charged until now | 2 s |  |
| 21312 | 04 | 1 | u16 | 0.01 | V | cpvolt_a | CP voltage | 2 s |  |
| 21313 | 04 | 1 | u16 | 0.1 | % | duty_a | PWM duty cycle | 2 s |  |
| 21314 | 04 | 1 | u16 |  |  | start_mode_a | Charging initialization mode | 2 s |  |
| 21315 | 04 | 1 | u16 |  |  | chargingfull_a | Fully charged indicator | 2 s |  |
| 21316 | 04 | 1 | u16 |  |  | setpowerflag_a | Power regulation switch | 2 s |  |
| 21317 | 04 | 1 | u16 |  |  | chgsts_a | Charging status | 2 s |  |
| 21318 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21320 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21322 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21331 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21332 | 04 | 5 | u16 |  |  |  |  | 2 s |  |
| 21338 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21340 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21341 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21342 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21343 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21344 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21345 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21346 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21348 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21350 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21351 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21352 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21353 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21354 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21355 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21356 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21358 | 04 | 2 | u32 |  |  |  |  | 2 s |  |
| 21360 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21362 | 04 | 1 | u16 |  |  |  |  | 2 s |  |
| 21211 | 03 | 1 | u16 |  |  |  |  | 1 h |  |
| 21232 | 03 | 1 | u16 |  |  |  |  | 1 h |  |
| 21234 | 03 | 16 | str |  |  |  |  | 1 h |  |
| 21250 | 03 | 32 | str |  |  |  |  | 1 h |  |
