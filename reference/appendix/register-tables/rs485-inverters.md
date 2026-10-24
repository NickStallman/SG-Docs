# RS-485 poll map: inverters (protocol types 1/4, 2, 3 and 5)

Everything the dongle reads from (or writes to) an inverter over RS-485, as a union of the four protocol-type tables. The `protocol` column lists which protocol types poll the row; the dongle picks the protocol type from the inverter's identity registers. `poll` is the nominal poll period; `capability w.b` means the row is only polled when bit b of word w of the 60-register capability block at address 5520 is set.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 306 of 847 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes | protocol |
|---|---|---|---|---|---|---|---|---|---|---|
| 2505 | 04 | 10 | str |  |  | dev_sn | Device S/N | 1 h |  | 5 |
| 2515 | 04 | 1 | u16 |  |  | devCode | devCode | 1 h |  | 1/4,2,3 |
| 2516 | 04 | 8 | str |  |  | devModel | devModel | 1 h |  | 1/4,2,3,5 |
| 2524 | 04 | 10 | str |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2534 | 04 | 2 | u32 |  |  | applyProtocolCode | applyProtocolCode | 1 h |  | 1/4,2,3,5 |
| 2536 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2551 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3 |
| 2553 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2555 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2565 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2567 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2569 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2571 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2577 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3 |
| 2579 | 04 | 2 | u32 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2582 | 04 | 120 | str |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 2702 | 04 | 60 | str |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 4874 | 10 | 1 | u16 |  |  | phys_addr |  |  |  | 1/4,2,3 |
| 4950 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4,2,3 |
| 4952 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4,2,3 |
| 4954 | 04 | 15 | str |  |  |  |  | every pass |  | 1/4,2,3 |
| 4969 | 04 | 15 | str |  |  |  |  | every pass |  | 1/4,2,3 |
| 4990 | 04 | 10 | str |  |  | dev_sn | Device S/N | every pass |  | 1/4 |
| 5000 | 03 | 1 | u16 |  |  |  |  | 1203 s |  | 1/4,2,3,5 |
| 5000 | 04 | 1 | u16 |  |  | devCode | devCode | every pass |  | 1/4,5 |
| 5001 | 03 | 1 | u16 |  |  |  |  | 1203 s |  | 1/4,2,3,5 |
| 5001 | 04 | 1 | u16 | 0.1 | kW | rated_power | Rated active power | every pass |  | 1/4,5 |
| 5002 | 03 | 1 | u16 |  |  |  |  | 1203 s |  | 1/4,2,3,5 |
| 5002 | 04 | 1 | u16 |  |  | feed_mode |  | every pass |  | 1/4,5 |
| 5003 | 03 | 1 | u16 |  |  |  |  | 1203 s |  | 1/4,2,3,5 |
| 5003 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily yield | every pass |  | 1/4,5 |
| 5004 | 03 | 1 | u16 |  |  |  |  | 1203 s |  | 1/4,2,3,5 |
| 5004 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4,5 |
| 5005 | 03 | 1 | u16 |  |  |  |  | 1203 s |  | 1/4,2,3,5 |
| 5006 | 10 | 1 | u16 |  |  |  |  |  |  | 1/4,2,3 |
| 5006 | 04 | 2 | u32 |  | h | total_run_time | Total on-grid operating time | every pass |  | 1/4,5 |
| 5007 | 10 | 2 | u32 |  |  |  |  |  |  | 1/4,2,3 |
| 5008 | 04 | 1 | s16 | 0.1 | ℃ | temp | Internal air temperature | every pass |  | 1/4,5 |
| 5009 | 04 | 2 | u32 | 0.001 | kVA | total_Apparent_rate | Total apparent power | every pass |  | 1/4 |
| 5011 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4,5 |
| 5012 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4,5 |
| 5013 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4,5 |
| 5014 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4,5 |
| 5015 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5016 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5017 | 04 | 2 | u32 | 0.001 | kW | total_direct_rate | Total DC power | every pass |  | 1/4,5 |
| 5019 | 10 | 1 | s16 |  |  |  |  |  |  | 1/4,2,3 |
| 5019 | 04 | 1 | u16 | 0.1 | V | ac_volt | AC voltage | every pass |  | 1/4,5 |
| 5020 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,5 |
| 5021 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,5 |
| 5022 | 04 | 1 | s16 | 0.1 | A | phase_current_A | Phase A current | every pass |  | 1/4 |
| 5023 | 04 | 1 | s16 | 0.1 | A | phase_current_B | Phase B current | every pass |  | 1/4 |
| 5024 | 04 | 1 | s16 | 0.1 | A | phase_current_C | Phase C current | every pass |  | 1/4 |
| 5031 | 04 | 2 | s32 | 0.001 | kW | curr_power | Total active power | every pass |  | 1/4 |
| 5033 | 04 | 2 | s32 | 0.001 | kvar | reactive_power | Total reactive power | every pass |  | 1/4,5 |
| 5035 | 04 | 1 | s16 | 0.001 |  | total_Factor_rate | Total power factor | every pass |  | 1/4,5 |
| 5036 | 04 | 1 | u16 | 0.1 | Hz | Frequency_rate | Grid frequency | every pass |  | 1/4,5 |
| 5036 | 10 | 2 | u32 |  |  |  |  |  |  | 1/4,2,3 |
| 5037 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5038 | 04 | 1 | u16 |  |  | dev_state | Device status | every pass |  | 1/4 |
| 5039 | 10 | 1 | u16 |  |  |  |  |  |  | 1/4,2,3 |
| 5039 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3 |
| 5040 | 10 | 1 | s16 |  |  |  |  |  |  | 1/4,2,3 |
| 5040 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3 |
| 5041 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3 |
| 5042 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3 |
| 5043 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3 |
| 5044 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3 |
| 5045 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5049 | 04 | 1 | u16 | 0.1 | kvar | rated_reactive | Rated reactive power | every pass |  | 1/4 |
| 5071 | 04 | 1 | u16 |  | kΩ | iso | Insulation impedance to ground | every pass |  | 1/4,5 |
| 5072 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5077 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5079 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4 |
| 5081 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5083 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4 |
| 5085 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4 |
| 5087 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4 |
| 5087 | 04 | 2 | u32 | 0.001 | kVA | total_Apparent_rate | Total apparent power | every pass |  | 5 |
| 5089 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4 |
| 5091 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4 |
| 5093 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5095 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5097 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5099 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5101 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5103 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5110 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5111 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5112 | 04 | 1 | u16 |  |  |  | AFCI fault count | every pass |  | 1/4 |
| 5113 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5114 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,5 |
| 5115 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5116 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5117 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5118 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5119 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5120 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5121 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5122 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5123 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5124 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5125 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5128 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5130 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5131 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5132 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5133 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5134 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5135 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5136 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 1/4 |
| 5137 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 1/4 |
| 5140 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5141 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5142 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5144 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4 |
| 5146 | 04 | 1 | s16 |  |  |  |  | every pass |  | 1/4 |
| 5147 | 04 | 1 | u16 | 0.1 | V | dc_bus_v | Bus voltage | every pass |  | 1/4,5 |
| 5148 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5150 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5151 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4 |
| 5200 | 04 | 14 | str |  |  |  |  | every pass |  | 2,3 |
| 5214 | 04 | 2 | s32 |  | W | battery_power | Battery power | every pass | capability 1.0 | 2,3 |
| 5216 | 04 | 2 | s32 | 0.001 | kW | supply_network_curr_power | Total feed-in active power | every pass | capability 1.1 | 2,3 |
| 5218 | 04 | 2 | s32 | 0.001 | kW | supply_load_curr_power | Total active power of load | every pass | capability 1.2 | 2,3 |
| 5220 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5221 | 04 | 1 | u16 |  |  | dev_state | Device status | every pass |  | 2,3 |
| 5222 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.3 | 2,3 |
| 5223 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily yield | every pass | capability 1.4 | 2,3 |
| 5224 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.5 | 2,3 |
| 5226 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.6 | 2,3 |
| 5228 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.7 | 2,3 |
| 5230 | 04 | 2 | u32 |  | h | total_run_time | Total on-grid operating time | every pass | capability 1.8 | 2,3 |
| 5232 | 04 | 2 | s32 | 0.001 | kW | curr_power | Total active power | every pass |  | 2,3 |
| 5234 | 04 | 2 | s32 | 0.001 | kvar | reactive_power | Total reactive power | every pass |  | 2,3 |
| 5236 | 04 | 2 | u32 | 0.001 | kW | total_direct_rate | Total DC power | every pass |  | 2,3 |
| 5238 | 04 | 2 | u32 | 0.001 | kVA | total_Apparent_rate | Total apparent power | every pass |  | 2,3 |
| 5240 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.9 | 2,3 |
| 5241 | 04 | 1 | s16 | 0.001 |  | total_Factor_rate | Total power factor | every pass |  | 2,3 |
| 5242 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5243 | 04 | 1 | s16 |  |  |  |  | every pass | capability 1.10 | 2,3 |
| 5244 | 04 | 1 | u16 | 0.1 | V | dc_bus_v | Bus voltage | every pass | capability 1.11 | 2,3 |
| 5245 | 04 | 1 | s16 | 0.1 | ℃ | temp | Internal air temperature | every pass |  | 2,3 |
| 5246 | 04 | 1 | u16 |  | kΩ | iso | Insulation impedance to ground | every pass |  | 2,3 |
| 5247 | 04 | 1 | u16 | 0.1 | V | ac_volt | AC voltage | every pass |  | 2,3 |
| 5248 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5249 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5250 | 04 | 1 | s16 | 0.1 | A | phase_current_A | Phase A current | every pass |  | 2,3 |
| 5251 | 04 | 1 | s16 | 0.1 | A | phase_current_B | Phase B current | every pass |  | 2,3 |
| 5252 | 04 | 1 | s16 | 0.1 | A | phase_current_C | Phase C current | every pass |  | 2,3 |
| 5253 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5254 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5255 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5256 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5257 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5258 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5259 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5260 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5261 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5262 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5263 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5264 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5265 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5266 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5267 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5268 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5269 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5270 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5271 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5272 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5273 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5274 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5275 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5276 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5277 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5278 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5279 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5280 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5281 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5282 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5283 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5284 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5285 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5286 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5287 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5288 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5289 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5290 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5291 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5292 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5293 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5294 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5295 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5296 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5297 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5298 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5299 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5300 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5301 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5302 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5303 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5304 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5305 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5306 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5307 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5308 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5309 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5310 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5311 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5312 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5313 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5314 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5315 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5316 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5317 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5318 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5319 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5320 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5321 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5322 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5323 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5324 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5325 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5326 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5327 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5328 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5329 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5330 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5331 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5332 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 2,3 |
| 5333 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5334 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5335 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5336 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5337 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5338 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5339 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5340 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5341 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5342 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5343 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5344 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5345 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5346 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5347 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5348 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5349 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5350 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5351 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5352 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5353 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5354 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5355 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5356 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5357 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5358 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5359 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5360 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5361 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5362 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5363 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5364 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5365 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5366 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5367 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5368 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5369 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5370 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5371 | 04 | 1 | u16 | 0.1 | V | voltage |  | every pass |  | 2,3 |
| 5372 | 04 | 1 | u16 | 0.1 | A | current |  | every pass |  | 2,3 |
| 5373 | 04 | 1 | u16 |  |  |  |  | every pass |  | 3 |
| 5374 | 04 | 1 | u16 | 0.1 | kW | rated_power | Rated active power | every pass |  | 2,3 |
| 5375 | 04 | 1 | u16 |  |  | feed_mode |  | every pass |  | 2,3 |
| 5376 | 04 | 1 | u16 | 0.1 | kvar | rated_reactive | Rated reactive power | every pass |  | 2,3 |
| 5377 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.12 | 2,3 |
| 5378 | 04 | 10 | str |  |  | dev_sn | Device S/N | every pass |  | 2,3 |
| 5388 | 04 | 15 | str |  |  |  |  | every pass | capability 1.13 | 2,3 |
| 5403 | 04 | 15 | str |  |  |  |  | every pass | capability 1.13 | 2,3 |
| 5418 | 04 | 15 | str |  |  |  |  | every pass | capability 1.13 | 2,3 |
| 5433 | 04 | 15 | str |  |  |  |  | every pass | capability 1.13 | 2,3 |
| 5448 | 04 | 25 | str |  |  |  |  | every pass |  | 2,3 |
| 5473 | 04 | 2 | u32 |  |  |  |  | every pass |  | 3 |
| 5475 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.1 | 2,3 |
| 5476 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.1 | 2,3 |
| 5477 | 04 | 2 | u32 |  |  |  |  | every pass |  | 3 |
| 5479 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5480 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5481 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5482 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5483 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5484 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5485 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5486 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.15 | 2,3 |
| 5487 | 04 | 2 | u32 |  |  | functionEnum | functionEnum | every pass |  | 2,3 |
| 5489 | 04 | 2 | u32 |  |  | functionEnum2 | functionEnum2 | every pass |  | 2,3 |
| 5491 | 04 | 4 | str |  |  |  |  | every pass |  | 3 |
| 5495 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.3 | 2,3 |
| 5496 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.3 | 2,3 |
| 5497 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.4 | 2,3 |
| 5498 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.5 | 2,3 |
| 5499 | 04 | 2 | u32 |  |  |  |  | every pass | capability 2.1 | 2,3 |
| 5501 | 04 | 2 | u32 |  |  |  |  | every pass | capability 2.1 | 2,3 |
| 5503 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.2 | 2,3 |
| 5504 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5505 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.14 | 2,3 |
| 5506 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.6 | 2,3 |
| 5507 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.7 | 2,3 |
| 5508 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.8 | 2,3 |
| 5509 | 04 | 2 | u32 |  |  |  |  | every pass | capability 2.0 | 2,3 |
| 5511 | 04 | 2 | s32 |  |  |  |  | every pass | capability 2.0 | 2,3 |
| 5513 | 04 | 1 | s16 |  |  |  |  | every pass | capability 2.0 | 2,3 |
| 5514 | 04 | 1 | u16 |  |  |  |  | every pass | capability 3.11 | 2,3 |
| 5515 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5516 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5517 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5518 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5519 | 04 | 1 | u16 |  |  |  | AFCI fault count | every pass | capability 4.1 | 2,3 |
| 5520 | 04 | 60 | str |  |  |  |  | every pass |  | 2,3 |
| 5580 | 04 | 21 | str |  |  |  |  | every pass |  | 3 |
| 5601 | 04 | 2 | s32 |  |  |  |  | every pass | capability 2.9 | 2,3 |
| 5603 | 04 | 2 | s32 |  |  |  |  | every pass | capability 2.9 | 2,3 |
| 5605 | 04 | 2 | s32 |  |  |  |  | every pass | capability 2.9 | 2,3 |
| 5607 | 04 | 2 | s32 |  |  |  |  | every pass | capability 2.9 | 2,3 |
| 5609 | 04 | 2 | u32 | 0.1 | kWh | supplynet_yeild | Feed-in energy today | every pass | capability 2.10 | 2,3 |
| 5611 | 04 | 2 | u32 | 0.1 | kWh | supplynet_total | Total feed-in energy | every pass | capability 2.10 | 2,3 |
| 5613 | 04 | 2 | u32 | 0.1 | kWh | network_yeild | Energy purchased today | every pass | capability 2.10 | 2,3 |
| 5615 | 04 | 2 | u32 | 0.1 | kWh | network_total | Total purchased energy | every pass | capability 2.10 | 2,3 |
| 5617 | 04 | 2 | u32 | 0.1 | kWh | daliy_directconsume_power | Daily PV energy consumed by loads | every pass | capability 2.10 | 2,3 |
| 5619 | 04 | 2 | u32 | 0.1 | kWh | tot_directconsume_power | Total PV energy consumed by loads | every pass | capability 2.10 | 2,3 |
| 5621 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.11 | 2,3 |
| 5622 | 04 | 1 | u16 |  |  |  |  |  |  | 1/4,2,3 |
| 5623 | 04 | 1 | u16 |  |  |  |  |  |  | 1/4,2,3 |
| 5624 | 04 | 1 | u16 |  |  |  |  | every pass | capability 3.8 | 2,3 |
| 5625 | 04 | 1 | u16 |  |  |  |  | every pass | capability 3.8 | 2,3 |
| 5626 | 04 | 1 | u16 |  |  |  |  | every pass | capability 3.9 | 2,3 |
| 5627 | 04 | 1 | u16 |  |  |  |  | every pass | capability 3.10 | 2,3 |
| 5628 | 04 | 1 | u16 |  |  |  |  |  |  | 1/4,2,3 |
| 5629 | 04 | 1 | u16 |  |  |  |  | every pass | capability 2.12 | 2,3 |
| 5630 | 04 | 1 | u16 | 0.1 | V | batt_voltage | Battery voltage | every pass | capability 2.13 | 2,3 |
| 5631 | 04 | 1 | s16 | 0.1 | A | batt_current | Battery current | every pass | capability 2.13 | 2,3 |
| 5632 | 04 | 1 | s16 | 0.1 | ℃ | batt_temp | Battery temperature | every pass | capability 2.13 | 2,3 |
| 5633 | 04 | 1 | u16 | 0.1 | % | batt_power | Battery level (SOC) | every pass | capability 2.13 | 2,3 |
| 5634 | 04 | 1 | u16 | 0.1 | % | batt_heathly | Battery SOH | every pass | capability 2.14 | 2,3 |
| 5635 | 04 | 1 | u16 |  | A | bms_charge_current | Max. charging current (BMS) |  |  | 1/4,2,3 |
| 5636 | 04 | 1 | u16 |  | A | bms_discharge_current | Max. discharging current (BMS) |  |  | 1/4,2,3 |
| 5637 | 04 | 2 | u32 |  | Wh | totbms_discharge_yeild | Total battery discharge (BMS) | every pass | capability 3.0 | 2,3 |
| 5639 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.11 | 2,3 |
| 5640 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.1 | 2,3 |
| 5641 | 04 | 2 | u32 |  |  |  |  | every pass | capability 6.1 | 2,3 |
| 5643 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.4 | 2,3 |
| 5644 | 04 | 15 | str |  |  |  |  | every pass |  | 3 |
| 5659 | 04 | 1 | u16 | 0.1 | kVA | apparent_power | Maximum apparent power | every pass |  | 2,3 |
| 5660 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily PV yield | every pass | capability 3.1 | 2,3 |
| 5661 | 04 | 2 | u32 | 0.1 | kWh | total_energy | Total PV yield | every pass | capability 3.1 | 2,3 |
| 5663 | 04 | 1 | u16 | 0.1 | kWh | deliy_supplynet_yeild | Feed-in energy today (PV) | every pass | capability 3.2 | 2,3 |
| 5664 | 04 | 2 | u32 | 0.1 | kWh | total_supplynet_yeild | Total feed-in energy (PV) | every pass | capability 3.2 | 2,3 |
| 5666 | 04 | 1 | u16 | 0.1 | kWh | battery_daliy_pv | PV-to-battery charge today | every pass | capability 3.3 | 2,3 |
| 5667 | 04 | 2 | u32 | 0.1 | kWh | battery_tot_pv | Total PV-to-battery charge | every pass | capability 3.3 | 2,3 |
| 5669 | 04 | 1 | u16 | 0.1 | kWh | battdaliy_discharge_yeild | Battery discharge today | every pass | capability 3.4 | 2,3 |
| 5670 | 04 | 2 | u32 | 0.1 | kWh | battdaliy_discharge_tot | Total battery discharge | every pass | capability 3.4 | 2,3 |
| 5672 | 04 | 1 | u16 | 0.1 | kWh | battdaliy_charge_yeild | Battery charge today | every pass | capability 3.5 | 2,3 |
| 5673 | 04 | 2 | u32 | 0.1 | kWh | battdtot_charge_yeild | Total battery charge | every pass | capability 3.5 | 2,3 |
| 5675 | 04 | 1 | u16 | 0.1 | % | deliy_self_rate | Daily self-consumption rate | every pass | capability 3.6 | 2,3 |
| 5676 | 04 | 1 | u16 | 0.1 | kWh | day_ac_out | Daily AC output yield | every pass | capability 3.12 | 2,3 |
| 5677 | 04 | 2 | u32 | 0.1 | kWh | total_ac_out | Total yield from AC output side | every pass | capability 3.12 | 2,3 |
| 5679 | 04 | 1 | u16 | 0.1 | kWh | day_ac_in | Daily energy purchased from AC input side | every pass | capability 3.13 | 2,3 |
| 5680 | 04 | 2 | u32 | 0.1 | kWh | total_ac_in | Total energy purchased from AC input side | every pass | capability 3.13 | 2,3 |
| 5682 | 04 | 37 | str |  |  |  |  | every pass |  | 3 |
| 5719 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.1 | 2,3 |
| 5720 | 04 | 1 | s16 | 0.1 | A | current | Phase A current of backup port | every pass | capability 5.0 | 2,3 |
| 5721 | 04 | 1 | s16 | 0.1 | A | current | Phase B current of backup port | every pass | capability 5.0 | 2,3 |
| 5722 | 04 | 1 | s16 | 0.1 | A | current | Phase C current of backup port | every pass | capability 5.0 | 2,3 |
| 5723 | 04 | 1 | s16 |  | W | rated_power | Phase A power of backup port | every pass | capability 5.0 | 2,3 |
| 5724 | 04 | 1 | s16 |  | W | rated_power | Phase B power of backup port | every pass | capability 5.0 | 2,3 |
| 5725 | 04 | 1 | s16 |  | W | rated_power | Phase C power of backup port | every pass | capability 5.0 | 2,3 |
| 5726 | 04 | 2 | s32 |  | W | rated_power | Total power of backup port | every pass | capability 5.0 | 2,3 |
| 5728 | 04 | 2 | u32 |  |  |  |  | every pass | capability 5.8 | 2,3 |
| 5730 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5731 | 04 | 1 | u16 | 0.1 | V | voltage | Phase A voltage of Backup port | every pass | capability 5.0 | 2,3 |
| 5732 | 04 | 1 | u16 | 0.1 | V | voltage | Phase B voltage of Backup port | every pass | capability 5.0 | 2,3 |
| 5733 | 04 | 1 | u16 | 0.1 | V | voltage | Phase C voltage of Backup port | every pass | capability 5.0 | 2,3 |
| 5734 | 04 | 1 | u16 | 0.01 | Hz | Frequency_rate | Backup port frequency | every pass | capability 5.0 | 2,3 |
| 5735 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.1 | 2,3 |
| 5736 | 04 | 1 | u16 |  |  |  |  | every pass | capability 41.8 | 2,3 |
| 5737 | 04 | 1 | u16 | 0.01 | Hz | meter_grid_frequency | Meter grid frequency | every pass | capability 5.6 | 2,3 |
| 5738 | 04 | 2 | s32 |  | var | reactive_power_meter | Reactive power uploaded by meter | every pass | capability 5.6 | 2,3 |
| 5740 | 04 | 1 | s16 |  |  |  |  | every pass | capability 5.6 | 2,3 |
| 5741 | 04 | 1 | u16 | 0.1 | V |  | Meter phase A voltage | every pass | capability 5.6 | 2,3 |
| 5742 | 04 | 1 | u16 | 0.1 | V |  | Meter phase B voltage | every pass | capability 5.6 | 2,3 |
| 5743 | 04 | 1 | u16 | 0.1 | V |  | Meter phase C voltage | every pass | capability 5.6 | 2,3 |
| 5744 | 04 | 1 | s16 | 0.01 | A | current_A_meter | Meter phase A current | every pass | capability 5.6 | 2,3 |
| 5745 | 04 | 1 | s16 | 0.01 | A | current_B_meter | Meter phase B current | every pass | capability 5.6 | 2,3 |
| 5746 | 04 | 1 | s16 | 0.01 | A | current_C_meter | Meter phase C current | every pass | capability 5.6 | 2,3 |
| 5747 | 04 | 2 | u32 |  |  |  |  | every pass | capability 5.6 | 2,3 |
| 5749 | 04 | 2 | u32 |  |  |  |  | every pass | capability 5.6 | 2,3 |
| 5751 | 04 | 2 | s32 |  |  |  |  | every pass |  | 2,3 |
| 5753 | 04 | 2 | s32 |  |  |  |  | every pass |  | 2,3 |
| 5755 | 04 | 2 | s32 |  |  |  |  | every pass |  | 2,3 |
| 5757 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5759 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5761 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5763 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5765 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5767 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5769 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5771 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5773 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5775 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5777 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5779 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5781 | 04 | 1 | s16 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5782 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5784 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5786 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5788 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5790 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5792 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5794 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5796 | 04 | 2 | s32 |  |  |  |  | every pass | capability 34.10 | 2,3 |
| 5798 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.5 | 2,3 |
| 5799 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.4 | 2,3 |
| 5800 | 04 | 15 | str |  |  | certify_swversion | Firmware version | every pass | capability 3.7 | 2,3 |
| 5815 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5830 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5845 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5860 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5875 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5890 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5905 | 04 | 15 | str |  |  |  |  | every pass | capability 3.7 | 2,3 |
| 5920 | 04 | 16 | str |  |  |  |  | every pass | capability 3.14 | 2,3 |
| 5936 | 04 | 32 | str |  |  |  |  | every pass | capability 4.0 | 2,3 |
| 5968 | 04 | 1 | u16 |  |  |  |  | every pass | capability 30.1 | 2,3 |
| 5969 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.2 | 2,3 |
| 5970 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.3 | 2,3 |
| 5971 | 04 | 2 | u32 |  |  |  |  | every pass |  | 3 |
| 5973 | 04 | 2 | u32 |  |  |  |  | every pass |  | 3 |
| 5975 | 04 | 1 | s16 |  |  |  |  | every pass | capability 4.4 | 2,3 |
| 5976 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.4 | 2,3 |
| 5977 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.4 | 2,3 |
| 5978 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.4 | 2,3 |
| 5979 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.5 | 2,3 |
| 5980 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.8 | 2,3 |
| 5981 | 04 | 7 | str |  |  |  |  | every pass |  | 3 |
| 5988 | 04 | 1 | s16 |  | r/min |  | External fan 1 speed | every pass | capability 6.6 | 2,3 |
| 5989 | 04 | 1 | s16 |  | r/min |  | External fan 2 speed | every pass | capability 6.6 | 2,3 |
| 5990 | 04 | 1 | str |  |  |  |  | every pass |  | 3 |
| 5991 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 5992 | 04 | 7 | str |  |  |  |  | every pass |  | 3 |
| 5999 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.5 | 2,3 |
| 6000 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.7 | 2,3 |
| 6001 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.7 | 2,3 |
| 6002 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.7 | 2,3 |
| 6003 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.7 | 2,3 |
| 6004 | 04 | 1 | u16 |  |  |  |  | every pass | capability 4.7 | 2,3 |
| 6005 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6006 | 04 | 1 | u16 |  |  |  |  | every pass |  | 3 |
| 6007 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.3 | 2,3 |
| 6008 | 04 | 12 | u16 |  |  |  |  | every pass |  | 3 |
| 6020 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.10 | 2,3 |
| 6021 | 04 | 30 | u16 |  |  |  |  | every pass |  | 3 |
| 6051 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6053 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6055 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6057 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6058 | 04 | 3 | str |  |  |  |  | every pass |  | 3 |
| 6061 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6063 | 04 | 16 | str |  |  |  |  | every pass |  | 3 |
| 6079 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6080 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6082 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6083 | 04 | 6 | str |  |  |  |  | every pass |  | 3 |
| 6089 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6091 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 6093 | 04 | 6 | str |  |  |  |  | every pass |  | 3 |
| 6099 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6200 | 04 | 2 | u32 |  |  |  |  | every pass | capability 9.1 | 2,3 |
| 6202 | 04 | 2 | u32 |  |  |  |  | every pass | capability 9.1 | 2,3 |
| 6303 | 04 | 31 | str |  |  |  |  | every pass |  | 3 |
| 6334 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6335 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6336 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6337 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6338 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6339 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6340 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6341 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6342 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6343 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6450 | 04 | 1 | u16 |  |  |  |  | every pass | capability 47.2 | 2,3 |
| 6451 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.11 | 2,3 |
| 6452 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.11 | 2,3 |
| 6453 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.12 | 2,3 |
| 6454 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.12 | 2,3 |
| 6455 | 04 | 1 | u16 |  |  |  |  | every pass |  | 3 |
| 6456 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6457 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6458 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6459 | 04 | 40 | str |  |  |  |  | every pass |  | 3 |
| 6499 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 6500 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.9 | 2,3 |
| 6501 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.9 | 2,3 |
| 6950 | 04 | 1 | u16 |  |  |  |  | every pass | capability 9.3 | 2,3 |
| 6951 | 04 | 1 | u16 |  |  |  |  | every pass | capability 9.4 | 2,3 |
| 7013 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7014 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7015 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7016 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7017 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7018 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7019 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7020 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7021 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7022 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7023 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7024 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7025 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7026 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7027 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7028 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7029 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7030 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7031 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7032 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7033 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7034 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7035 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7036 | 04 | 1 | u16 | 0.01 | A | current |  | every pass |  | 1/4 |
| 7100 | 04 | 16 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7196 | 04 | 31 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7227 | 04 | 12 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7239 | 04 | 40 | u32 |  |  |  |  | every pass |  | 2,3 |
| 7279 | 04 | 96 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7375 | 04 | 31 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7406 | 04 | 12 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7418 | 04 | 40 | u32 |  |  |  |  | every pass |  | 2,3 |
| 7458 | 04 | 96 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7554 | 04 | 31 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7585 | 04 | 12 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7597 | 04 | 40 | u32 |  |  |  |  | every pass |  | 2,3 |
| 7637 | 04 | 96 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7733 | 04 | 31 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7764 | 04 | 12 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7776 | 04 | 40 | u32 |  |  |  |  | every pass |  | 2,3 |
| 7816 | 04 | 2 | u32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7818 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7820 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7822 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7824 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7826 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7828 | 04 | 2 | s32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7830 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7831 | 04 | 2 | s32 |  |  |  |  | every pass | capability 6.7 | 2,3 |
| 7833 | 04 | 20 | u16 |  |  |  |  | every pass |  | 3 |
| 7853 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7854 | 04 | 1 | u16 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 7855 | 04 | 2 | str |  |  |  |  | every pass |  | 3 |
| 7857 | 04 | 1 | u16 |  |  |  |  | every pass | capability 50.3 | 2,3 |
| 7858 | 04 | 2 | str |  |  |  |  | every pass |  | 3 |
| 7860 | 04 | 20 | str |  |  |  |  | every pass |  | 2,3 |
| 7880 | 04 | 2 | s32 |  |  |  |  | every pass | capability 7.14 | 2,3 |
| 7882 | 04 | 2 | s32 |  |  |  |  | every pass | capability 7.14 | 2,3 |
| 7884 | 04 | 2 | s32 |  |  |  |  | every pass | capability 7.14 | 2,3 |
| 7886 | 04 | 2 | s32 |  |  |  |  | every pass | capability 7.14 | 2,3 |
| 7888 | 04 | 2 | s32 |  |  |  |  | every pass | capability 7.14 | 2,3 |
| 7890 | 04 | 1 | u16 |  |  |  |  | every pass | capability 6.11 | 2,3 |
| 7891 | 04 | 2 | str |  |  |  |  | every pass |  | 3 |
| 7893 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7894 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7897 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7898 | 04 | 1 | u16 |  |  |  |  | every pass |  | 3 |
| 7899 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7900 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7901 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7902 | 04 | 1 | u16 |  |  |  |  | every pass |  | 3 |
| 7903 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7904 | 04 | 1 | u16 |  |  |  |  | every pass |  | 3 |
| 7905 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7906 | 04 | 4 | str |  |  |  |  | every pass |  | 3 |
| 7910 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7911 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7912 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 7914 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7915 | 04 | 9 | str |  |  |  |  | every pass |  | 3 |
| 7924 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 7999 | 04 | 1 | s16 |  |  |  |  | every pass | capability 44.1 | 2,3 |
| 8000 | 04 | 1 | s16 |  |  |  |  | every pass | capability 44.2 | 2,3 |
| 8001 | 04 | 1 | s16 |  |  |  |  | every pass | capability 44.3 | 2,3 |
| 8002 | 04 | 1 | u16 |  |  |  |  | every pass | capability 44.1 | 2,3 |
| 8003 | 04 | 1 | u16 |  |  |  |  | every pass | capability 44.2 | 2,3 |
| 8004 | 04 | 1 | u16 |  |  |  |  | every pass | capability 44.3 | 2,3 |
| 8005 | 04 | 2 | s32 |  |  |  |  | every pass | capability 44.1 | 2,3 |
| 8007 | 04 | 2 | s32 |  |  |  |  | every pass | capability 44.2 | 2,3 |
| 8009 | 04 | 2 | s32 |  |  |  |  | every pass | capability 44.3 | 2,3 |
| 8011 | 04 | 2 | s32 |  |  |  |  | every pass |  | 3 |
| 8013 | 04 | 1 | u16 |  |  |  |  | every pass | capability 44.5 | 2,3 |
| 8014 | 04 | 22 | str |  |  |  |  | every pass |  | 3 |
| 8036 | 04 | 1 | u16 |  |  |  |  | every pass | capability 44.0 | 2,3 |
| 8037 | 04 | 1 | u16 |  |  |  |  | every pass | capability 46.4 | 2,3 |
| 8038 | 04 | 2 | u32 |  |  |  |  | every pass | capability 46.12 | 2,3 |
| 8040 | 04 | 1 | u16 |  |  |  |  | every pass | capability 46.13 | 2,3 |
| 8041 | 04 | 1 | u16 |  |  |  |  | every pass | capability 46.14 | 2,3 |
| 8042 | 04 | 5 | str |  |  |  |  | every pass |  | 3 |
| 8047 | 04 | 1 | u16 |  |  |  |  | every pass | capability 50.14 | 2,3 |
| 8048 | 04 | 1 | u16 |  |  |  |  | every pass | capability 50.14 | 2,3 |
| 8049 | 04 | 1 | u16 |  |  |  |  | every pass | capability 50.14 | 2,3 |
| 8050 | 04 | 1 | u16 |  |  |  |  | every pass | capability 50.14 | 2,3 |
| 8100 | 04 | 41 | str |  |  |  |  | every pass |  | 1/4 |
| 8150 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 8151 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 8152 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 8153 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 8154 | 04 | 2 | s32 |  |  |  |  | every pass |  | 2,3 |
| 8156 | 04 | 2 | s32 |  |  |  |  | every pass |  | 2,3 |
| 8158 | 04 | 2 | u32 |  |  |  |  | every pass |  | 2,3 |
| 8160 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 8161 | 04 | 1 | u16 |  |  |  |  | every pass | capability 40.6 | 2,3 |
| 8162 | 04 | 1 | u16 |  |  |  |  | every pass | capability 40.6 | 2,3 |
| 8163 | 04 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 8164 | 04 | 9 | u16 |  |  |  |  | every pass |  | 3 |
| 8173 | 04 | 2 | s32 |  |  |  |  | every pass | capability 8.5 | 2,3 |
| 8175 | 04 | 2 | s32 |  |  |  |  | every pass | capability 8.5 | 2,3 |
| 8177 | 04 | 2 | s32 |  |  |  |  | every pass | capability 8.5 | 2,3 |
| 8179 | 04 | 21 | u16 |  |  |  |  | every pass |  | 3 |
| 8200 | 04 | 1 | u16 |  |  |  |  | every pass | capability 9.0 | 2,3 |
| 8201 | 04 | 2 | u32 |  |  |  |  | every pass | capability 9.0 | 2,3 |
| 8203 | 04 | 10 | u16 |  |  |  |  | every pass |  | 3 |
| 8213 | 04 | 1 | s16 | 0.1 | V | l1_volt | L1-to-ground voltage | every pass | capability 9.7 | 2,3 |
| 8214 | 04 | 1 | s16 | 0.1 | V | l2_volt | L2-to-ground voltage | every pass | capability 9.7 | 2,3 |
| 8215 | 04 | 1 | s16 | 0.1 | V | l3_volt | L3-to-ground voltage | every pass | capability 9.7 | 2,3 |
| 8801 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8816 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8831 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8846 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8861 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8876 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8891 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 8906 | 04 | 15 | str |  |  |  |  | every pass | capability 46.11 | 2,3 |
| 10301 | 04 | 10 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10311 | 04 | 15 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10326 | 04 | 10 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10336 | 04 | 15 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10351 | 04 | 10 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10361 | 04 | 15 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10376 | 04 | 10 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10386 | 04 | 15 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10401 | 04 | 10 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10411 | 04 | 15 | str |  |  |  |  | 1 h | capability 4.12 | 2,3 |
| 10451 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.3 | 2,3 |
| 10452 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.3 | 2,3 |
| 10453 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.3 | 2,3 |
| 10454 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.3 | 2,3 |
| 10455 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.3 | 2,3 |
| 10456 | 04 | 1 | u16 |  |  |  |  | every pass | capability 8.2 | 2,3 |
| 10457 | 04 | 1 | u16 |  |  |  |  | every pass | capability 46.5 | 2,3 |
| 10458 | 04 | 1 | u16 |  |  |  |  | every pass | capability 46.5 | 2,3 |
| 10752 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.10 | 2,3 |
| 10753 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.10 | 2,3 |
| 10754 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.10 | 2,3 |
| 10755 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.10 | 2,3 |
| 10756 | 04 | 1 | u16 |  |  |  |  | every pass | capability 5.10 | 2,3 |
| 13000 | 04 | 1 | u16 |  |  | dev_state | Device status | every pass |  | 5 |
| 13001 | 04 | 1 | u16 |  |  | batt_state | Battery status | every pass |  | 5 |
| 13002 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily PV yield | every pass |  | 5 |
| 13003 | 04 | 2 | u32 | 0.1 | kWh | total_energy | Total PV yield | every pass |  | 5 |
| 13005 | 04 | 1 | u16 | 0.1 | kWh | deliy_supplynet_yeild | Feed-in energy today (PV) | every pass |  | 5 |
| 13006 | 04 | 2 | u32 | 0.1 | kWh | total_supplynet_yeild | Total feed-in energy (PV) | every pass |  | 5 |
| 13008 | 04 | 2 | s32 | 0.001 | kW | supply_load_curr_power | Total active power of load | every pass |  | 5 |
| 13010 | 04 | 2 | s32 | 0.001 | kW | supply_network_curr_power | Total feed-in active power | every pass |  | 5 |
| 13012 | 04 | 1 | u16 | 0.1 | kWh | battery_daliy_pv | PV-to-battery charge today | every pass |  | 5 |
| 13013 | 04 | 2 | u32 | 0.1 | kWh | battery_tot_pv | Total PV-to-battery charge | every pass |  | 5 |
| 13015 | 04 | 2 | u32 |  |  |  |  | every pass |  | 5 |
| 13017 | 04 | 1 | u16 | 0.1 | kWh | daliy_directconsume_power | Daily PV energy consumed by loads | every pass |  | 5 |
| 13018 | 04 | 2 | u32 | 0.1 | kWh | tot_directconsume_power | Total PV energy consumed by loads | every pass |  | 5 |
| 13020 | 04 | 1 | u16 | 0.1 | V | batt_voltage | Battery voltage | every pass |  | 5 |
| 13021 | 04 | 1 | s16 | 0.1 | A | batt_current | Battery current | every pass |  | 5 |
| 13022 | 04 | 1 | u16 |  | W | battery_power | Battery power | every pass |  | 5 |
| 13023 | 04 | 1 | u16 | 0.1 | % | batt_power | Battery level (SOC) | every pass |  | 5 |
| 13024 | 04 | 1 | u16 | 0.1 | % | batt_heathly | Battery SOH | every pass |  | 5 |
| 13025 | 04 | 1 | s16 | 0.1 | ℃ | batt_temp | Battery temperature | every pass |  | 5 |
| 13026 | 04 | 1 | u16 | 0.1 | kWh | battdaliy_discharge_yeild | Battery discharge today | every pass |  | 5 |
| 13027 | 04 | 2 | u32 | 0.1 | kWh | battdaliy_discharge_tot | Total battery discharge | every pass |  | 5 |
| 13029 | 04 | 1 | u16 | 0.1 | % | deliy_self_rate | Daily self-consumption rate | every pass |  | 5 |
| 13030 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13031 | 04 | 1 | s16 | 0.1 | A | phase_current_A | Phase A current | every pass |  | 5 |
| 13034 | 04 | 2 | s32 | 0.001 | kW | curr_power | Total active power | every pass |  | 5 |
| 13036 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13037 | 04 | 2 | u32 | 0.1 | kWh | network_total | Total purchased energy | every pass |  | 5 |
| 13039 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13040 | 04 | 1 | u16 | 0.1 | kWh | battdaliy_charge_yeild | Battery charge today | every pass |  | 5 |
| 13041 | 04 | 2 | u32 | 0.1 | kWh | battdtot_charge_yeild | Total battery charge | every pass |  | 5 |
| 13043 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13045 | 04 | 1 | u16 | 0.1 | kWh | supplynet_yeild | Feed-in energy today | every pass |  | 5 |
| 13046 | 04 | 2 | u32 | 0.1 | kWh | supplynet_total | Total feed-in energy | every pass |  | 5 |
| 13050 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13052 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13054 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13055 | 03 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13056 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13058 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13060 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13062 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13064 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13066 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13068 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13070 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13072 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13074 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13076 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13078 | 04 | 2 | u32 |  |  |  |  | every pass | 32 bits | 5 |
| 13101 | 04 | 1 | u16 |  | A | bms_charge_current | Max. charging current (BMS) | every pass |  | 5 |
| 13102 | 04 | 1 | u16 |  | A | bms_discharge_current | Max. discharging current (BMS) | every pass |  | 5 |
| 13103 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13104 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13105 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13106 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13107 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13108 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13109 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13110 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13111 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13112 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13113 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13114 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13115 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13116 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13117 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13118 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13119 | 04 | 2 | u32 |  | Wh | totbms_discharge_yeild | Total battery discharge (BMS) | every pass |  | 5 |
| 13130 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13131 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13132 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13133 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13134 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13135 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13136 | 04 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13152 | 03 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 13153 | 03 | 1 | u16 |  |  |  |  | every pass |  | 5 |
| 14503 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14573 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14574 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14575 | 77 | 2 | u32 |  |  |  |  | 3 s |  | 2,3 |
| 14577 | 77 | 4 | u64 |  |  |  |  | 3 s |  | 2,3 |
| 14581 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14582 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14583 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14585 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14586 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14587 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14588 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14589 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14590 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14591 | 77 | 1 | u16 |  |  | opt_install_type | Optimizer installation type | 3 s |  | 2,3 |
| 14592 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14594 | 77 | 1 | u16 |  |  |  |  | 3 s |  | 2,3 |
| 14701 | 77 | 25 | str |  |  |  |  |  |  | 2,3 |
| 30035 | 10 | 1 | u16 |  |  | phys_addr |  |  |  | 5 |
| 30051 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 1/4,2,3,5 |
| 30105 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 47.13 | 2,3 |
| 30111 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 32.1 | 2,3 |
| 30114 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 21.2 | 2,3 |
| 30115 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 21.3 | 2,3 |
| 30644 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 1/4 |
| 31034 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.15 | 2 |
| 31035 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 31036 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.11 | 2 |
| 31037 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 5.12 | 2 |
| 31200 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 23.0 | 2,3 |
| 31201 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 23.0 | 2,3 |
| 31202 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 23.0 | 2,3 |
| 31204 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 31205 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 31208 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 37.0 | 2,3 |
| 31209 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 23.7 | 2,3 |
| 31210 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 23.7 | 2,3 |
| 31213 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 23.1 | 2,3 |
| 31214 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 36.11 | 2 |
| 31215 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 36.11 | 2 |
| 31216 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.5 | 2,3 |
| 31219 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.6 | 2 |
| 31221 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 28.4 | 2,3 |
| 31225 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 1/4,2,3 |
| 31227 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 31228 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 36.13 | 2,3 |
| 31452 | 03 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 31461 | 03 | 1 | u16 |  |  |  |  | every pass |  | 2,3 |
| 31997 | 03 | 2 | u32 |  |  | functionEnum | functionEnum | 1 h |  | 1/4 |
| 32003 | 03 | 2 | u32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 32005 | 03 | 2 | u32 |  |  |  |  | every pass |  | 1/4,2,3,5 |
| 32254 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 29.5 | 2,3 |
| 32260 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 29.5 | 2,3 |
| 32303 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 32304 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 32305 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 32306 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 32307 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.7 | 2 |
| 32311 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 1/4,2,3 |
| 32538 | 03 | 1 | u16 |  |  | net_id | PLC network ID | 1 h |  | 1/4,2,3 |
| 32547 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 32617 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 32618 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2 |
| 32698 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 41.13 | 2,3 |
| 32907 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.10 | 2 |
| 32908 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.12 | 2 |
| 32909 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 36.8 | 2 |
| 32910 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 36.14 | 2 |
| 33002 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 24.3 | 2,3 |
| 33017 | 03 | 1 | u16 |  |  |  |  | 60 s |  | 2,3 |
| 33018 | 03 | 1 | u16 |  |  |  |  | 60 s |  | 2,3 |
| 33031 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 24.12 | 2,3 |
| 33032 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 24.12 | 2,3 |
| 33050 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 35.1 | 2,3 |
| 33060 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.11 | 2,3 |
| 33061 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.11 | 2,3 |
| 33062 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.11 | 2,3 |
| 33144 | 03 | 1 | u16 |  |  | remote_server | Connecting to remote server | 1 h |  | 2,3 |
| 33145 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 33146 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 25.4 | 2,3 |
| 33147 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 33148 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 33208 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 25.12 | 2,3 |
| 33276 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2,3 |
| 33277 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2,3 |
| 33278 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2,3 |
| 33279 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2,3 |
| 33280 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2,3 |
| 33283 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.5 | 2,3 |
| 33284 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2 |
| 33285 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2 |
| 33286 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2 |
| 33287 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2 |
| 33288 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 30.9 | 2 |
| 33500 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 29.6 | 2,3 |
| 33501 | 03 | 1 | u16 |  |  |  |  | 60 s | capability 29.6 | 2,3 |
| 33503 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 37.1 | 2,3 |
| 33509 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 39.15 | 2,3 |
| 33539 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33540 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33541 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33542 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33543 | 03 | 2 | u32 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33545 | 03 | 1 | u16 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33546 | 03 | 2 | u32 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33548 | 03 | 2 | u32 |  |  |  |  | 1 h | capability 34.3 | 2,3 |
| 33550 | 03 | 1 | u16 |  |  |  |  | 30 s | capability 34.3 | 2,3 |
| 34209 | 03 | 16 | str |  |  | dev_model | Device model | 1 h |  | 2,3 |
| 34781 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
| 34782 | 03 | 1 | u16 |  |  |  |  | 1 h |  | 2,3 |
