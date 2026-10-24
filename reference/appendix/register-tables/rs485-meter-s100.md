# RS-485 poll map: meter type 6300 (S100)

Built-in point table for an S100 meter connected directly to the dongle's RS-485 bus.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 8 of 8 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes |
|---|---|---|---|---|---|---|---|---|---|
| 4097 | 04 | 2 | f32 | 0.1 | V | phase_voltage_A | Phase A voltage | every pass |  |
| 4109 | 04 | 2 | f32 | 0.001 | A | phase_current_A | Phase A current | every pass |  |
| 4115 | 04 | 2 | f32 | 0.001 | kW | Phase A active power | Phase A active power | every pass |  |
| 4121 | 04 | 2 | f32 | 0.0001 | kW | curr_power | Active power | every pass |  |
| 4141 | 04 | 2 | f32 | 0.001 |  | power_factor | PF | every pass |  |
| 4143 | 04 | 2 | f32 | 0.01 | Hz | grid_frequence | Grid frequency | every pass |  |
| 4145 | 04 | 2 | f32 | 0.01 | kWh | pos_act_power | Forward active energy | every pass |  |
| 4147 | 04 | 2 | f32 | 0.01 | kWh | neg_act_power | Reverse active energy | every pass |  |
