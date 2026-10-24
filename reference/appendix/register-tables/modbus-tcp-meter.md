# Modbus TCP map: meter and Energy Bridge (device type 18)

Registers the dongle serves over Modbus TCP for a meter device.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 26 of 34 rows are named.

| address | fn | count | type | scale | unit | field | label | notes |
|---|---|---|---|---|---|---|---|---|
| 4097 | 04 | 2 | f32 | 0.1 | V | phase_voltage_A | Phase A voltage |  |
| 4099 | 04 | 2 | f32 | 0.1 | V | phase_voltage_B | Phase B voltage |  |
| 4101 | 04 | 2 | f32 | 0.1 | V | phase_voltage_C | Phase C voltage |  |
| 4103 | 04 | 2 | f32 | 0.1 | V | line_voltage_AB | A-B line voltage |  |
| 4105 | 04 | 2 | f32 | 0.1 | V | line_voltage_BC | B-C line voltage |  |
| 4107 | 04 | 2 | f32 | 0.1 | V | line_voltage_CA | C-A line voltage |  |
| 4109 | 04 | 2 | f32 | 0.001 | A | phase_current_A | Phase A current |  |
| 4111 | 04 | 2 | f32 | 0.001 | A | phase_current_B | Phase B current |  |
| 4113 | 04 | 2 | f32 | 0.001 | A | phase_current_C | Phase C current |  |
| 4115 | 04 | 2 | f32 | 0.001 | kW | Phase A active power | Phase A active power |  |
| 4117 | 04 | 2 | f32 | 0.001 | kW | Phase B active power | Phase B active power |  |
| 4119 | 04 | 2 | f32 | 0.001 | kW | Phase C active power | Phase C active power |  |
| 4121 | 04 | 2 | f32 | 0.0001 | kW | curr_power | Active power |  |
| 4123 | 04 | 2 | f32 | 0.0001 | kW | neg_curr_power | Feed-in power |  |
| 4125 | 04 | 2 | f32 |  |  |  |  |  |
| 4127 | 04 | 2 | f32 |  |  |  |  |  |
| 4129 | 04 | 2 | f32 |  |  |  |  |  |
| 4131 | 04 | 2 | f32 | 0.0001 | kvar | reactive_power | Reactive power |  |
| 4133 | 04 | 2 | f32 |  |  |  |  |  |
| 4135 | 04 | 2 | f32 |  |  |  |  |  |
| 4137 | 04 | 2 | f32 |  |  |  |  |  |
| 4139 | 04 | 2 | f32 | 0.0001 | kVA | apparent_power | Apparent power |  |
| 4141 | 04 | 2 | f32 | 0.001 |  | power_factor | PF |  |
| 4143 | 04 | 2 | f32 | 0.01 | Hz | grid_frequence | Grid frequency |  |
| 4145 | 04 | 2 | f32 | 0.01 | kWh | pos_act_power | Forward active energy |  |
| 4147 | 04 | 2 | f32 | 0.01 | kWh | neg_act_power | Reverse active energy |  |
| 4149 | 04 | 2 | f32 | 0.01 | kvarh | pos_react_power | Forward reactive energy |  |
| 4151 | 04 | 2 | f32 | 0.01 | kvarh | neg_react_power | Reverse reactive energy |  |
| 4153 | 04 | 1 | u16 |  |  |  |  |  |
| 4154 | 04 | 1 | u16 |  |  |  |  |  |
| 4155 | 04 | 2 | f32 | 0.0001 | kW | neg_curr_power | Load power |  |
| 4157 | 04 | 2 | f32 | 0.001 |  | power_factor_A |  |  |
| 4159 | 04 | 2 | f32 | 0.001 |  | power_factor_B |  |  |
| 4161 | 04 | 2 | f32 | 0.001 |  | power_factor_C |  |  |
