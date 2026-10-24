# RS-485 poll map: meter type 6100 (DTSD1352)

Built-in point table for a DTSD1352 meter connected directly to the dongle's RS-485 bus.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 32 of 40 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes |
|---|---|---|---|---|---|---|---|---|---|
| 4097 | 04 | 2 | f32 | 0.1 | V | phase_voltage_A | Phase A voltage | every pass |  |
| 4099 | 04 | 2 | f32 | 0.1 | V | phase_voltage_B | Phase B voltage | every pass |  |
| 4101 | 04 | 2 | f32 | 0.1 | V | phase_voltage_C | Phase C voltage | every pass |  |
| 4103 | 04 | 2 | f32 | 0.1 | V | line_voltage_AB | A-B line voltage | every pass |  |
| 4105 | 04 | 2 | f32 | 0.1 | V | line_voltage_BC | B-C line voltage | every pass |  |
| 4107 | 04 | 2 | f32 | 0.1 | V | line_voltage_CA | C-A line voltage | every pass |  |
| 4109 | 04 | 2 | f32 | 0.001 | A | phase_current_A | Phase A current | every pass |  |
| 4111 | 04 | 2 | f32 | 0.001 | A | phase_current_B | Phase B current | every pass |  |
| 4113 | 04 | 2 | f32 | 0.001 | A | phase_current_C | Phase C current | every pass |  |
| 4115 | 04 | 2 | f32 | 0.001 | kW | Phase A active power | Phase A active power | every pass |  |
| 4117 | 04 | 2 | f32 | 0.001 | kW | Phase B active power | Phase B active power | every pass |  |
| 4119 | 04 | 2 | f32 | 0.001 | kW | Phase C active power | Phase C active power | every pass |  |
| 4121 | 04 | 2 | f32 | 0.0001 | kW | curr_power | Active power | every pass |  |
| 4123 | 04 | 2 | f32 | 0.0001 | kW | neg_curr_power | Feed-in power | every pass |  |
| 4125 | 04 | 2 | f32 |  |  |  |  | every pass |  |
| 4127 | 04 | 2 | f32 |  |  |  |  | every pass |  |
| 4129 | 04 | 2 | f32 |  |  |  |  | every pass |  |
| 4131 | 04 | 2 | f32 | 0.0001 | kvar | reactive_power | Reactive power | every pass |  |
| 4133 | 04 | 2 | f32 |  |  |  |  | every pass |  |
| 4135 | 04 | 2 | f32 |  |  |  |  | every pass |  |
| 4137 | 04 | 2 | f32 |  |  |  |  | every pass |  |
| 4139 | 04 | 2 | f32 | 0.0001 | kVA | apparent_power | Apparent power | every pass |  |
| 4141 | 04 | 2 | f32 | 0.001 |  | power_factor | PF | every pass |  |
| 4143 | 04 | 2 | f32 | 0.01 | Hz | grid_frequence | Grid frequency | every pass |  |
| 4145 | 04 | 2 | f32 | 0.01 | kWh | pos_act_power | Forward active energy | every pass |  |
| 4147 | 04 | 2 | f32 | 0.01 | kWh | neg_act_power | Reverse active energy | every pass |  |
| 4149 | 04 | 2 | f32 | 0.01 | kvarh | pos_react_power | Forward reactive energy | every pass |  |
| 4151 | 04 | 2 | f32 | 0.01 | kvarh | neg_react_power | Reverse reactive energy | every pass |  |
| 4153 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 4154 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 4155 | 04 | 2 | f32 | 0.0001 | kW | neg_curr_power | Load power | every pass |  |
| 4157 | 04 | 2 | f32 | 0.001 |  | power_factor_A |  | every pass |  |
| 4159 | 04 | 2 | f32 | 0.001 |  | power_factor_B |  | every pass |  |
| 4161 | 04 | 2 | f32 | 0.001 |  | power_factor_C |  | every pass |  |
| 4161 | 04 | 2 | f32 | 0.01 | kWh | active_electric_energy_A | Phase A forward active energy | every pass |  |
| 4163 | 04 | 2 | f32 | 0.01 | kWh | active_electric_energy_B | Phase B forward active energy | every pass |  |
| 4165 | 04 | 2 | f32 | 0.01 | kWh | active_electric_energy_C | Phase B forward active energy | every pass |  |
| 4167 | 04 | 2 | f32 | 0.01 | kWh | reversing_electric_energy_A | Reverse active electric energy of phase A | every pass |  |
| 4169 | 04 | 2 | f32 | 0.01 | kWh | reversing_electric_energy_B | Reverse active electric energy of phase B | every pass |  |
| 4171 | 04 | 2 | f32 | 0.01 | kWh | reversing_electric_energy_C | Reverse active electric energy of phase C | every pass |  |
