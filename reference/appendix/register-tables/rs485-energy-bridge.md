# RS-485 poll map: Energy Bridge (device type 92)

Registers defined for an Energy Bridge reached through the inverter.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 41 of 46 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes |
|---|---|---|---|---|---|---|---|---|---|
| 4096 | 04 | 1 | u16 |  |  | protocal_version | Protocol version No. | 3 s |  |
| 4097 | 04 | 10 | str |  |  | dev_sn | Device S/N | 3 s |  |
| 4107 | 04 | 8 | str |  |  |  |  | 3 s |  |
| 4117 | 04 | 10 | str |  |  | dev_vendor | Device manufacturer | 3 s |  |
| 4127 | 04 | 1 | u16 |  |  | dev_code | dev_code | 3 s |  |
| 4128 | 04 | 10 | str |  |  | firmware_version | Firmware version | 3 s |  |
| 4161 | 04 | 1 | u16 |  |  | switch_state | Switch status | every pass |  |
| 4162 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 4163 | 04 | 1 | u16 | 0.1 | V | volt_l1 | Phase A voltage | every pass |  |
| 4164 | 04 | 1 | u16 | 0.1 | V | volt_l2 | Phase B voltage | every pass |  |
| 4165 | 04 | 1 | u16 | 0.1 | V | volt_l3 | Phase C voltage | every pass |  |
| 4166 | 04 | 1 | s16 | 0.1 | ° | grid_inv_phase_diff_l1_n | Grid/inverter A-N phase difference | every pass |  |
| 4167 | 04 | 1 | s16 | 0.1 | V | grid_inv_amplitude_diff_l1_n | Grid/inverter A-N magnitude difference | every pass |  |
| 4168 | 04 | 1 | s16 | 0.1 | ° | grid_phase_diff_l2_l1 | Grid phase B-A difference | every pass |  |
| 4169 | 04 | 1 | s16 | 0.1 | ° | grid_phase_diff_l3_l1 | Grid phase C-A difference | every pass |  |
| 4170 | 04 | 1 | u16 | 0.01 | Hz | grid_grid_freq | Grid frequency | every pass |  |
| 4171 | 04 | 2 | s32 | 0.001 | kW | line1_total_actice_pow | Channel 1 total active power | every pass |  |
| 4173 | 04 | 2 | s32 | 0.001 | kW | line1_actice_pow_l1 | Channel 1 phase A active power | every pass |  |
| 4175 | 04 | 2 | s32 | 0.001 | kW | line1_actice_pow_l2 | Channel 1 phase B active power | every pass |  |
| 4177 | 04 | 2 | s32 | 0.001 | kW | line1_actice_pow_l3 | Channel 1 phase C active power | every pass |  |
| 4179 | 04 | 1 | u16 | 0.01 | A | line1_current_l1 | Channel 1 phase A current | every pass |  |
| 4180 | 04 | 1 | u16 | 0.01 | A | line1_current_l2 | Channel 1 phase B current | every pass |  |
| 4181 | 04 | 1 | u16 | 0.01 | A | line1_current_l3 | Channel 1 phase C current | every pass |  |
| 4182 | 04 | 2 | s32 | 0.001 | kW | line2_total_actice_pow | Channel 2 total active power | every pass |  |
| 4184 | 04 | 2 | s32 | 0.001 | kW | line2_actice_pow_l1 | Channel 2 phase A active power | every pass |  |
| 4186 | 04 | 2 | s32 | 0.001 | kW | line2_actice_pow_l2 | Channel 2 phase B active power | every pass |  |
| 4188 | 04 | 2 | s32 | 0.001 | kW | line2_actice_pow_l3 | Channel 2 phase C active power | every pass |  |
| 4190 | 04 | 1 | u16 | 0.01 | A | line2_current_l1 | Channel 2 phase A current | every pass |  |
| 4191 | 04 | 1 | u16 | 0.01 | A | line2_current_l2 | Channel 2 phase B current | every pass |  |
| 4192 | 04 | 1 | u16 | 0.01 | A | line2_current_l3 | Channel 2 phase C current | every pass |  |
| 4193 | 04 | 2 | s32 | 0.001 | kvar | line1_total_reactice_pow | Channel 1 total reactive power | every pass |  |
| 4195 | 04 | 2 | s32 | 0.001 | kvar | line1_reactice_pow_l1 | Channel 1 phase A reactive power | every pass |  |
| 4197 | 04 | 2 | s32 | 0.001 | kvar | line1_reactice_pow_l2 | Channel 1 phase B reactive power | every pass |  |
| 4199 | 04 | 2 | s32 | 0.001 | kvar | line1_reactice_pow_l3 | Channel 1 phase C reactive power | every pass |  |
| 4201 | 04 | 2 | s32 | 0.001 | kvar | line2_total_reactice_pow | Channel 2 total reactive power | every pass |  |
| 4203 | 04 | 2 | s32 | 0.001 | kvar | line2_reactice_pow_l1 | Channel 2 phase A reactive power | every pass |  |
| 4205 | 04 | 2 | s32 | 0.001 | kvar | line2_reactice_pow_l2 | Channel 2 phase B reactive power | every pass |  |
| 4207 | 04 | 2 | s32 | 0.001 | kvar | line2_reactice_pow_l3 | Channel 2 phase C reactive power | every pass |  |
| 4209 | 04 | 2 | u32 | 0.01 | kWh | line1_positive_tot_active_energy | Channel 1 forward total active energy | every pass |  |
| 4211 | 04 | 2 | u32 | 0.01 | kWh | line1_reverse_tot_active_energy | Channel 1 reverse total active energy | every pass |  |
| 4213 | 04 | 2 | u32 | 0.01 | kWh | line2_positive_tot_active_energy | Channel 2 total forward active energy | every pass |  |
| 4215 | 04 | 2 | u32 | 0.01 | kWh | line2_reverse_tot_active_energy | Channel 2 total reverse active energy | every pass |  |
| 4217 | 04 | 1 | u16 |  |  | breaker_trip_num | Tripping times | every pass |  |
| 4218 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 4220 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 4221 | 04 | 1 | u16 |  |  |  |  | every pass |  |
