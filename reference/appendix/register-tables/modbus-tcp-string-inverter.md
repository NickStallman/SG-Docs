# Modbus TCP map: string inverter (device types 1 and 21)

Registers the dongle serves over Modbus TCP for a string inverter. Values come from the dongle's poll cache; unmapped read-holding addresses are forwarded to the inverter live.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 278 of 498 rows are named.

| address | fn | count | type | scale | unit | field | label | notes |
|---|---|---|---|---|---|---|---|---|
| 2515 | 04 | 1 | u16 |  |  | devCode | devCode |  |
| 2516 | 04 | 8 | str |  |  | devModel | devModel |  |
| 2524 | 04 | 10 | str |  |  |  |  |  |
| 2534 | 04 | 2 | u32 |  |  | applyProtocolCode | applyProtocolCode |  |
| 2536 | 04 | 2 | u32 |  |  |  |  |  |
| 2551 | 04 | 2 | u32 |  |  |  |  |  |
| 2553 | 04 | 2 | u32 |  |  |  |  |  |
| 2555 | 04 | 2 | u32 |  |  |  |  |  |
| 2565 | 04 | 2 | u32 |  |  |  |  |  |
| 2567 | 04 | 2 | u32 |  |  |  |  |  |
| 2577 | 04 | 2 | u32 |  |  |  |  |  |
| 2579 | 04 | 2 | u32 |  |  |  |  |  |
| 2582 | 04 | 120 | str |  |  |  |  |  |
| 2702 | 04 | 60 | str |  |  |  |  |  |
| 4950 | 04 | 2 | u32 |  |  |  |  |  |
| 4952 | 04 | 2 | u32 |  |  |  |  |  |
| 4954 | 04 | 15 | str |  |  |  |  |  |
| 4969 | 04 | 15 | str |  |  |  |  |  |
| 4990 | 04 | 10 | str |  |  | dev_sn | Device S/N |  |
| 5000 | 04 | 1 | u16 |  |  | devCode | devCode |  |
| 5001 | 04 | 1 | u16 | 0.1 | kW | rated_power | Rated active power |  |
| 5002 | 04 | 1 | u16 |  |  | feed_mode |  |  |
| 5003 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily yield |  |
| 5004 | 04 | 2 | u32 |  |  |  |  |  |
| 5006 | 04 | 2 | u32 |  | h | total_run_time | Total on-grid operating time |  |
| 5008 | 04 | 1 | s16 | 0.1 | ℃ | temp | Internal air temperature |  |
| 5009 | 04 | 2 | u32 | 0.001 | kVA | total_Apparent_rate | Total apparent power |  |
| 5011 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5012 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5013 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5014 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5015 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5016 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5017 | 04 | 2 | u32 | 0.001 | kW | total_direct_rate | Total DC power |  |
| 5019 | 04 | 1 | u16 | 0.1 | V | ac_volt | AC voltage |  |
| 5020 | 04 | 1 | u16 |  |  |  |  |  |
| 5021 | 04 | 1 | u16 |  |  |  |  |  |
| 5022 | 04 | 1 | s16 | 0.1 | A | phase_current_A | Phase A current |  |
| 5023 | 04 | 1 | s16 | 0.1 | A | phase_current_B | Phase B current |  |
| 5024 | 04 | 1 | s16 | 0.1 | A | phase_current_C | Phase C current |  |
| 5031 | 04 | 2 | s32 | 0.001 | kW | curr_power | Total active power |  |
| 5033 | 04 | 2 | s32 | 0.001 | kvar | reactive_power | Total reactive power |  |
| 5035 | 04 | 1 | s16 | 0.001 |  | power_factor | Total power factor |  |
| 5036 | 04 | 1 | u16 |  |  |  |  |  |
| 5037 | 04 | 1 | u16 |  |  |  |  |  |
| 5038 | 04 | 1 | u16 |  |  | dev_state | Operating status |  |
| 5039 | 04 | 1 | u16 |  |  |  |  |  |
| 5040 | 04 | 1 | u16 |  |  |  |  |  |
| 5041 | 04 | 1 | u16 |  |  |  |  |  |
| 5042 | 04 | 1 | u16 |  |  |  |  |  |
| 5043 | 04 | 1 | u16 |  |  |  |  |  |
| 5044 | 04 | 1 | u16 |  |  |  |  |  |
| 5045 | 04 | 1 | u16 |  |  |  |  |  |
| 5049 | 04 | 1 | u16 | 0.1 | kvar | rated_reactive | Rated reactive power |  |
| 5071 | 04 | 1 | u16 |  | kΩ | iso | Insulation impedance to ground |  |
| 5072 | 04 | 1 | u16 |  |  |  |  |  |
| 5077 | 04 | 2 | u32 |  |  |  |  |  |
| 5079 | 04 | 2 | s32 |  |  |  |  |  |
| 5081 | 04 | 2 | u32 |  |  |  |  |  |
| 5083 | 04 | 2 | s32 |  |  |  |  |  |
| 5085 | 04 | 2 | s32 |  |  |  |  |  |
| 5087 | 04 | 2 | s32 |  |  |  |  |  |
| 5089 | 04 | 2 | s32 |  |  |  |  |  |
| 5091 | 04 | 2 | s32 |  |  |  |  |  |
| 5093 | 04 | 2 | u32 | 0.1 | kWh | supplynet_yeild | Feed-in energy today |  |
| 5095 | 04 | 2 | u32 | 0.1 | kWh | supplynet_total | Total feed-in energy |  |
| 5097 | 04 | 2 | u32 | 0.1 | kWh | network_yeild | Energy purchased today |  |
| 5099 | 04 | 2 | u32 | 0.1 | kWh | network_total | Total purchased energy |  |
| 5101 | 04 | 2 | u32 | 0.1 | kWh | daliy_directconsume_power | Daily PV energy consumed by loads |  |
| 5103 | 04 | 2 | u32 | 0.1 | kWh | tot_directconsume_power | Total PV energy consumed by loads |  |
| 5113 | 04 | 1 | u16 |  |  |  |  |  |
| 5114 | 04 | 1 | u16 |  |  |  |  |  |
| 5115 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5116 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5117 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5118 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5119 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5120 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5121 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5122 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5123 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5124 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5125 | 04 | 1 | u16 |  |  |  |  |  |
| 5128 | 04 | 2 | u32 |  |  |  |  |  |
| 5130 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5131 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5132 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5133 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5134 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5135 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5136 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5137 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5140 | 04 | 1 | u16 |  |  |  |  |  |
| 5141 | 04 | 1 | u16 |  |  |  |  |  |
| 5142 | 04 | 1 | u16 |  |  |  |  |  |
| 5144 | 04 | 2 | u32 |  |  |  |  |  |
| 5146 | 04 | 1 | s16 |  |  |  |  |  |
| 5147 | 04 | 1 | u16 | 0.1 | V | dc_bus_v | Bus voltage |  |
| 5148 | 04 | 1 | u16 |  |  |  |  |  |
| 5150 | 04 | 1 | u16 |  |  |  |  |  |
| 5151 | 04 | 1 | u16 |  |  |  |  |  |
| 5150 | 04 | 1 | u16 |  |  |  |  |  |
| 5151 | 04 | 1 | u16 |  |  |  |  |  |
| 5186 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5187 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5188 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5189 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5190 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5191 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5192 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5193 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 7013 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7014 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7015 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7016 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7017 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7018 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7019 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7020 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7021 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7022 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7023 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7024 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7025 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7026 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7027 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7028 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7029 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7030 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7031 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7032 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7033 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7034 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7035 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7036 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7037 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7038 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7039 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7040 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7041 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7042 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7043 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7044 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5200 | 04 | 14 | str |  |  |  |  |  |
| 5214 | 04 | 2 | s32 |  |  |  |  |  |
| 5216 | 04 | 2 | s32 |  | W | supply_network_curr_power | Total feed-in active power |  |
| 5218 | 04 | 2 | s32 |  | W | supply_load_curr_power | Total active power of load |  |
| 5220 | 04 | 1 | u16 |  |  |  |  |  |
| 5221 | 04 | 1 | u16 |  |  | dev_state | Operating status |  |
| 5222 | 04 | 1 | u16 |  |  |  |  |  |
| 5223 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily yield |  |
| 5224 | 04 | 2 | u32 |  |  |  |  |  |
| 5226 | 04 | 2 | u32 |  |  |  |  |  |
| 5228 | 04 | 2 | u32 |  |  |  |  |  |
| 5230 | 04 | 2 | u32 |  | h | total_run_time | Total on-grid operating time |  |
| 5232 | 04 | 2 | s32 | 0.001 | kW | curr_power | Total active power |  |
| 5234 | 04 | 2 | s32 | 0.001 | kvar | reactive_power | Total reactive power |  |
| 5236 | 04 | 2 | u32 | 0.001 | kW | total_direct_rate | Total DC power |  |
| 5238 | 04 | 2 | u32 | 0.001 | kVA | total_Apparent_rate | Total apparent power |  |
| 5240 | 04 | 1 | u16 |  |  |  |  |  |
| 5241 | 04 | 1 | s16 | 0.001 |  | power_factor | Total power factor |  |
| 5242 | 04 | 1 | u16 |  |  |  |  |  |
| 5243 | 04 | 1 | s16 |  |  |  |  |  |
| 5244 | 04 | 1 | u16 | 0.1 | V | dc_bus_v | Bus voltage |  |
| 5245 | 04 | 1 | s16 | 0.1 | ℃ | temp | Internal air temperature |  |
| 5246 | 04 | 1 | u16 |  | kΩ | iso | Insulation impedance to ground |  |
| 5247 | 04 | 1 | u16 | 0.1 | V | ac_volt | AC voltage |  |
| 5248 | 04 | 1 | u16 |  |  |  |  |  |
| 5249 | 04 | 1 | u16 |  |  |  |  |  |
| 5250 | 04 | 1 | s16 | 0.1 | A | phase_current_A | Phase A current |  |
| 5251 | 04 | 1 | s16 | 0.1 | A | phase_current_B | Phase B current |  |
| 5252 | 04 | 1 | s16 | 0.1 | A | phase_current_C | Phase C current |  |
| 5253 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5254 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5255 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5256 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5257 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5258 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5259 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5260 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5261 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5262 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5263 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5264 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5265 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5266 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5267 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5268 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5269 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5270 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5271 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5272 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5273 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5274 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5275 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5276 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5277 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5278 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5279 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5280 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5281 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5282 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5283 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5284 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5285 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5286 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5287 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5288 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5289 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5290 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5291 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5292 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5293 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5294 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5295 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5296 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5297 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5298 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5299 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5300 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5301 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5302 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5303 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5304 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5305 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5306 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5307 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5308 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5309 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5310 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5311 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5312 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5313 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5314 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5315 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5316 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5317 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5318 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5319 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5320 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5321 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5322 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5323 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5324 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5325 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5326 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5327 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5328 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5329 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5330 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5331 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5332 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 5333 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5334 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5335 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5336 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5337 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5338 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5339 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5340 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5341 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5342 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5343 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5344 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5345 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5346 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5347 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5348 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5349 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5350 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5351 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5352 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5353 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5354 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5355 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5356 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5357 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5358 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5359 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5360 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5361 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5362 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5363 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5364 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5365 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5366 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5367 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5368 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5369 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5370 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5371 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5372 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5373 | 04 | 1 | u16 |  |  | devCode | devCode |  |
| 5374 | 04 | 1 | u16 | 0.1 | kW | rated_power | Rated active power |  |
| 5375 | 04 | 1 | u16 |  |  | feed_mode |  |  |
| 5376 | 04 | 1 | u16 | 0.1 | kvar | rated_reactive | Rated reactive power |  |
| 5377 | 04 | 1 | u16 |  |  |  |  |  |
| 5378 | 04 | 10 | str |  |  | dev_sn | Device S/N |  |
| 5388 | 04 | 15 | str |  |  |  |  |  |
| 5403 | 04 | 15 | str |  |  |  |  |  |
| 5418 | 04 | 15 | str |  |  |  |  |  |
| 5433 | 04 | 15 | str |  |  |  |  |  |
| 5448 | 04 | 25 | str |  |  |  |  |  |
| 5475 | 04 | 1 | u16 |  |  |  |  |  |
| 5476 | 04 | 1 | u16 |  |  |  |  |  |
| 5479 | 04 | 1 | u16 |  |  |  |  |  |
| 5480 | 04 | 1 | u16 |  |  |  |  |  |
| 5481 | 04 | 1 | u16 |  |  |  |  |  |
| 5482 | 04 | 1 | u16 |  |  |  |  |  |
| 5483 | 04 | 1 | u16 |  |  |  |  |  |
| 5484 | 04 | 1 | u16 |  |  |  |  |  |
| 5485 | 04 | 1 | u16 |  |  |  |  |  |
| 5486 | 04 | 1 | u16 |  |  |  |  |  |
| 5487 | 04 | 2 | u32 |  |  | functionEnum | functionEnum |  |
| 5489 | 04 | 2 | u32 |  |  | functionEnum2 | functionEnum2 |  |
| 5495 | 04 | 1 | u16 |  |  |  |  |  |
| 5496 | 04 | 1 | u16 |  |  |  |  |  |
| 5497 | 04 | 1 | u16 |  |  |  |  |  |
| 5498 | 04 | 1 | u16 |  |  |  |  |  |
| 5499 | 04 | 2 | u32 |  |  |  |  |  |
| 5501 | 04 | 2 | u32 |  |  |  |  |  |
| 5503 | 04 | 1 | u16 |  |  |  |  |  |
| 5505 | 04 | 1 | u16 |  |  |  |  |  |
| 5506 | 04 | 1 | u16 |  |  |  |  |  |
| 5507 | 04 | 1 | u16 |  |  |  |  |  |
| 5508 | 04 | 1 | u16 |  |  |  |  |  |
| 5509 | 04 | 2 | u32 |  |  |  |  |  |
| 5511 | 04 | 2 | s32 |  |  |  |  |  |
| 5513 | 04 | 1 | s16 |  |  |  |  |  |
| 5514 | 04 | 1 | u16 |  |  |  |  |  |
| 5520 | 04 | 60 | str |  |  |  |  |  |
| 5601 | 04 | 2 | s32 |  |  |  |  |  |
| 5603 | 04 | 2 | s32 |  |  |  |  |  |
| 5605 | 04 | 2 | s32 |  |  |  |  |  |
| 5607 | 04 | 2 | s32 |  |  |  |  |  |
| 5609 | 04 | 2 | u32 | 0.1 | kWh | supplynet_yeild | Feed-in energy today |  |
| 5611 | 04 | 2 | u32 | 0.1 | kWh | supplynet_total | Total feed-in energy |  |
| 5613 | 04 | 2 | u32 | 0.1 | kWh | network_yeild | Energy purchased today |  |
| 5615 | 04 | 2 | u32 | 0.1 | kWh | network_total | Total purchased energy |  |
| 5617 | 04 | 2 | u32 | 0.1 | kWh | daliy_directconsume_power | Daily PV energy consumed by loads |  |
| 5619 | 04 | 2 | u32 | 0.1 | kWh | tot_directconsume_power | Total PV energy consumed by loads |  |
| 5622 | 04 | 1 | u16 |  |  |  |  |  |
| 5623 | 04 | 1 | u16 |  |  |  |  |  |
| 5624 | 04 | 1 | u16 |  |  |  |  |  |
| 5625 | 04 | 1 | u16 |  |  |  |  |  |
| 5626 | 04 | 1 | u16 |  |  |  |  |  |
| 5627 | 04 | 1 | u16 |  |  |  |  |  |
| 5628 | 04 | 1 | u16 |  |  |  |  |  |
| 5629 | 04 | 1 | u16 |  |  |  |  |  |
| 5630 | 04 | 1 | u16 |  |  |  |  |  |
| 5631 | 04 | 1 | s16 |  |  |  |  |  |
| 5632 | 04 | 1 | s16 |  |  |  |  |  |
| 5633 | 04 | 1 | u16 |  |  |  |  |  |
| 5634 | 04 | 1 | u16 |  |  |  |  |  |
| 5635 | 04 | 1 | u16 |  |  |  |  |  |
| 5636 | 04 | 1 | u16 |  |  |  |  |  |
| 5637 | 04 | 2 | u32 |  |  |  |  |  |
| 5640 | 04 | 1 | u16 |  |  |  |  |  |
| 5641 | 04 | 2 | u32 |  |  |  |  |  |
| 5643 | 04 | 1 | u16 |  |  |  |  |  |
| 5660 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily PV yield |  |
| 5661 | 04 | 2 | u32 | 0.1 | kWh | total_energy | Total PV yield |  |
| 5663 | 04 | 1 | u16 | 0.1 | kWh | deliy_supplynet_yeild | Feed-in energy today (PV) |  |
| 5664 | 04 | 2 | u32 | 0.1 | kWh | total_supplynet_yeild | Total feed-in energy (PV) |  |
| 5666 | 04 | 1 | u16 |  |  |  |  |  |
| 5667 | 04 | 2 | u32 |  |  |  |  |  |
| 5669 | 04 | 1 | u16 |  |  |  |  |  |
| 5670 | 04 | 2 | u32 |  |  |  |  |  |
| 5672 | 04 | 1 | u16 |  |  |  |  |  |
| 5673 | 04 | 2 | u32 |  |  |  |  |  |
| 5675 | 04 | 1 | u16 | 0.1 | % | deliy_self_rate | Daily self-consumption rate |  |
| 5676 | 04 | 1 | u16 | 0.1 | kWh | day_ac_out | Daily AC output yield |  |
| 5677 | 04 | 2 | u32 | 0.1 | kWh | total_ac_out | Total yield from AC output side |  |
| 5679 | 04 | 1 | u16 | 0.1 | kWh | day_ac_in | Daily energy purchased from AC input side |  |
| 5680 | 04 | 2 | u32 | 0.1 | kWh | total_ac_in | Total energy purchased from AC input side |  |
| 5730 | 04 | 1 | u16 |  |  |  |  |  |
| 5738 | 04 | 2 | s32 |  |  |  |  |  |
| 5740 | 04 | 1 | s16 |  |  |  |  |  |
| 5800 | 04 | 15 | str |  |  | certify_swversion | Firmware version |  |
| 5815 | 04 | 15 | str |  |  |  |  |  |
| 5830 | 04 | 15 | str |  |  |  |  |  |
| 5845 | 04 | 15 | str |  |  |  |  |  |
| 5860 | 04 | 15 | str |  |  |  |  |  |
| 5875 | 04 | 15 | str |  |  |  |  |  |
| 5890 | 04 | 15 | str |  |  |  |  |  |
| 5905 | 04 | 15 | str |  |  |  |  |  |
| 5920 | 04 | 16 | str |  |  |  |  |  |
| 5936 | 04 | 32 | str |  |  |  |  |  |
| 5999 | 04 | 1 | u16 |  |  |  |  |  |
| 6007 | 04 | 1 | u16 |  |  |  |  |  |
| 7816 | 04 | 2 | u32 |  |  |  |  |  |
| 7818 | 04 | 2 | u32 |  |  |  |  |  |
| 7828 | 04 | 2 | u32 |  |  |  |  |  |
| 7919 | 04 | 1 | u16 |  |  |  |  |  |
| 8047 | 04 | 1 | u16 |  |  |  |  |  |
| 8048 | 04 | 1 | u16 |  |  |  |  |  |
| 8049 | 04 | 1 | u16 |  |  |  |  |  |
| 8050 | 04 | 1 | u16 |  |  |  |  |  |
| 8150 | 04 | 1 | u16 |  |  |  |  |  |
| 8151 | 04 | 1 | u16 |  |  |  |  |  |
| 8152 | 04 | 1 | u16 |  |  |  |  |  |
| 8153 | 04 | 1 | u16 |  |  |  |  |  |
| 8156 | 04 | 2 | s32 |  |  |  |  |  |
| 8158 | 04 | 2 | u32 |  |  |  |  |  |
| 8160 | 04 | 1 | u16 |  |  |  |  |  |
| 8173 | 04 | 2 | s32 |  |  |  |  |  |
| 8175 | 04 | 2 | s32 |  |  |  |  |  |
| 8177 | 04 | 2 | s32 |  |  |  |  |  |
| 30051 | 03 | 1 | u16 |  |  |  |  |  |
| 31034 | 03 | 1 | u16 |  |  |  |  |  |
| 31035 | 03 | 1 | u16 |  |  |  |  |  |
| 31036 | 03 | 1 | u16 |  |  |  |  |  |
| 31037 | 03 | 1 | u16 |  |  |  |  |  |
| 31204 | 03 | 1 | u16 |  |  |  |  |  |
| 31205 | 03 | 1 | u16 |  |  |  |  |  |
| 31213 | 03 | 1 | u16 |  |  |  |  |  |
| 31216 | 03 | 1 | u16 |  |  |  |  |  |
| 31221 | 03 | 1 | u16 |  |  |  |  |  |
| 31227 | 03 | 1 | u16 |  |  |  |  |  |
| 31452 | 03 | 1 | u16 |  |  |  |  |  |
| 31997 | 03 | 2 | u32 |  |  |  |  |  |
| 32303 | 03 | 1 | u16 |  |  |  |  |  |
| 32304 | 03 | 1 | u16 |  |  |  |  |  |
| 32305 | 03 | 1 | u16 |  |  |  |  |  |
| 32306 | 03 | 1 | u16 |  |  |  |  |  |
| 32907 | 03 | 1 | u16 |  |  |  |  |  |
| 32909 | 03 | 1 | u16 |  |  |  |  |  |
| 32910 | 03 | 1 | u16 |  |  |  |  |  |
| 33002 | 03 | 1 | u16 |  |  |  |  |  |
| 33050 | 03 | 1 | u16 |  |  |  |  |  |
| 33146 | 03 | 1 | u16 |  |  |  |  |  |
| 34300 | 03 | 32 | str |  |  |  |  |  |
| 34332 | 03 | 10 | str |  |  |  |  |  |
| 34342 | 03 | 1 | u16 |  |  |  |  |  |
| 34343 | 03 | 8 | str |  |  |  |  |  |
| 34351 | 03 | 15 | str |  |  |  |  |  |
| 34366 | 03 | 15 | str |  |  |  |  |  |
| 34382 | 03 | 1 | u16 |  |  |  |  |  |
| 34781 | 03 | 1 | u16 |  |  |  |  |  |
| 34782 | 03 | 1 | u16 |  |  |  |  |  |
| 33509 | 03 | 1 | u16 |  |  |  |  |  |
| 32311 | 03 | 1 | u16 |  |  |  |  |  |
| 4874 | 10 | 1 | u16 |  |  | phys_addr |  |  |
| 5006 | 10 | 1 | u16 |  |  |  |  |  |
| 5007 | 10 | 2 | u32 |  |  |  |  |  |
| 5039 | 10 | 1 | u16 |  |  |  |  |  |
| 5036 | 10 | 2 | u32 |  |  |  |  |  |
| 5040 | 10 | 1 | s16 |  |  |  |  |  |
| 5019 | 10 | 1 | s16 |  |  |  |  |  |
| 6005 | 04 | 1 | u16 |  |  |  |  |  |
| 7013 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7014 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7015 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7016 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7017 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7018 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7019 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7020 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7021 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7022 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7023 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7024 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7025 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7026 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7027 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7028 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7029 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7030 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7031 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7032 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7033 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7034 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7035 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7036 | 04 | 1 | u16 | 0.01 | A | current |  |  |
| 7100 | 04 | 16 | u16 |  |  |  |  |  |
| 7196 | 04 | 31 | u16 |  |  |  |  |  |
| 7227 | 04 | 12 | u16 |  |  |  |  |  |
| 7239 | 04 | 40 | u32 |  |  |  |  |  |
| 7279 | 04 | 96 | u16 |  |  |  |  |  |
| 7375 | 04 | 31 | u16 |  |  |  |  |  |
| 7406 | 04 | 12 | u16 |  |  |  |  |  |
| 7418 | 04 | 40 | u32 |  |  |  |  |  |
| 7458 | 04 | 96 | u16 |  |  |  |  |  |
| 7554 | 04 | 31 | u16 |  |  |  |  |  |
| 7585 | 04 | 12 | u16 |  |  |  |  |  |
| 7597 | 04 | 40 | u32 |  |  |  |  |  |
| 7637 | 04 | 96 | u16 |  |  |  |  |  |
| 7733 | 04 | 31 | u16 |  |  |  |  |  |
| 7764 | 04 | 12 | u16 |  |  |  |  |  |
| 7776 | 04 | 40 | u32 |  |  |  |  |  |
| 7860 | 04 | 20 | str |  |  |  |  |  |
| 7890 | 04 | 1 | u16 |  |  |  |  |  |
| 30644 | 03 | 1 | u16 |  |  |  |  |  |
| 33284 | 03 | 1 | u16 |  |  |  |  |  |
| 33285 | 03 | 1 | u16 |  |  |  |  |  |
| 33286 | 03 | 1 | u16 |  |  |  |  |  |
| 33287 | 03 | 1 | u16 |  |  |  |  |  |
| 33288 | 03 | 1 | u16 |  |  |  |  |  |
