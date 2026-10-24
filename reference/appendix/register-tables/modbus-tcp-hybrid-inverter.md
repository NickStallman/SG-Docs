# Modbus TCP map: hybrid (storage) inverter (device type 35)

Registers the dongle serves over Modbus TCP for a hybrid inverter. Values come from the dongle's poll cache; unmapped read-holding addresses are forwarded to the inverter live.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 70 of 279 rows are named.

| address | fn | count | type | scale | unit | field | label | notes |
|---|---|---|---|---|---|---|---|---|
| 2505 | 04 | 10 | str |  |  | dev_sn | Device S/N |  |
| 2516 | 04 | 8 | str |  |  | devModel | devModel |  |
| 2524 | 04 | 10 | str |  |  |  |  |  |
| 2534 | 04 | 2 | u32 |  |  | applyProtocolCode | applyProtocolCode |  |
| 2536 | 04 | 2 | u32 |  |  |  |  |  |
| 2551 | 04 | 2 | u32 |  |  |  |  |  |
| 2553 | 04 | 2 | u32 |  |  |  |  |  |
| 2555 | 04 | 2 | u32 |  |  |  |  |  |
| 2565 | 04 | 2 | u32 |  |  |  |  |  |
| 2567 | 04 | 2 | u32 |  |  |  |  |  |
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
| 5003 | 04 | 1 | u16 | 0.1 | kWh | day_ac_out | Daily AC output yield |  |
| 5004 | 04 | 2 | u32 | 0.1 | kWh | total_ac_out | Total yield from AC output side |  |
| 5006 | 04 | 2 | u32 |  | h | total_run_time | Total on-grid operating time |  |
| 5008 | 04 | 1 | s16 | 0.1 | ℃ | temp | Internal air temperature |  |
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
| 5033 | 04 | 2 | s32 | 0.001 | kvar | reactive_power | Total reactive power |  |
| 5035 | 04 | 1 | u16 | 0.001 |  | total_Factor_rate | Total power factor |  |
| 5036 | 04 | 1 | u16 |  |  |  |  |  |
| 5115 | 04 | 1 | u16 | 0.1 | V | voltage |  |  |
| 5116 | 04 | 1 | u16 | 0.1 | A | current |  |  |
| 5214 | 04 | 2 | s32 |  |  |  |  |  |
| 5223 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily yield |  |
| 5228 | 04 | 2 | u32 |  |  |  |  |  |
| 5242 | 04 | 1 | u16 |  |  |  |  |  |
| 5377 | 04 | 1 | u16 |  |  |  |  |  |
| 5087 | 04 | 2 | s32 | 0.001 | kVA | total_Apparent_rate | Total apparent power |  |
| 5506 | 04 | 1 | u16 |  |  |  |  |  |
| 5631 | 04 | 1 | s16 | 0.1 | A | batt_current | Battery current |  |
| 5639 | 04 | 1 | u16 |  |  |  |  |  |
| 5448 | 04 | 25 | str |  |  |  |  |  |
| 5475 | 04 | 1 | u16 |  |  |  |  |  |
| 5476 | 04 | 1 | u16 |  |  |  |  |  |
| 5479 | 04 | 1 | u16 |  |  |  |  |  |
| 5485 | 04 | 1 | u16 |  |  |  |  |  |
| 5486 | 04 | 1 | u16 |  |  |  |  |  |
| 5487 | 04 | 2 | u32 |  |  | functionEnum | functionEnum |  |
| 5520 | 04 | 60 | str |  |  |  |  |  |
| 5640 | 04 | 1 | u16 |  |  |  |  |  |
| 5641 | 04 | 2 | u32 |  |  |  |  |  |
| 5643 | 04 | 1 | u16 |  |  |  |  |  |
| 5601 | 04 | 2 | s32 |  |  |  |  |  |
| 5603 | 04 | 2 | s32 |  |  |  |  |  |
| 5605 | 04 | 2 | s32 |  |  |  |  |  |
| 5607 | 04 | 2 | s32 |  |  |  |  |  |
| 5622 | 04 | 1 | u16 |  |  |  |  |  |
| 5623 | 04 | 1 | u16 |  |  |  |  |  |
| 5628 | 04 | 1 | u16 |  |  |  |  |  |
| 5635 | 04 | 1 | u16 |  | A | bms_charge_current | Max. charging current (BMS) |  |
| 5636 | 04 | 1 | u16 |  | A | bms_discharge_current | Max. discharging current (BMS) |  |
| 5720 | 04 | 1 | s16 | 0.1 | A | current | Phase A current of backup port |  |
| 5721 | 04 | 1 | s16 | 0.1 | A | current | Phase B current of backup port |  |
| 5722 | 04 | 1 | s16 | 0.1 | A | current | Phase C current of backup port |  |
| 5723 | 04 | 1 | s16 |  | W | rated_power | Phase A power of backup port |  |
| 5724 | 04 | 1 | s16 |  | W | rated_power | Phase B power of backup port |  |
| 5725 | 04 | 1 | s16 |  | W | rated_power | Phase C power of backup port |  |
| 5726 | 04 | 2 | s32 |  | W | rated_power | Total power of backup port |  |
| 5730 | 04 | 1 | u16 |  |  |  |  |  |
| 5731 | 04 | 1 | u16 | 0.1 | V | voltage | Phase A voltage of Backup port |  |
| 5732 | 04 | 1 | u16 | 0.1 | V | voltage | Phase B voltage of Backup port |  |
| 5733 | 04 | 1 | u16 | 0.1 | V | voltage | Phase C voltage of Backup port |  |
| 5734 | 04 | 1 | u16 | 0.01 | Hz | Frequency_rate | Backup port frequency |  |
| 5738 | 04 | 2 | s32 |  | var | reactive_power_meter | Reactive power uploaded by meter |  |
| 5740 | 04 | 1 | s16 |  |  |  |  |  |
| 5920 | 04 | 16 | str |  |  |  |  |  |
| 5936 | 04 | 32 | str |  |  |  |  |  |
| 5999 | 04 | 1 | s16 |  |  |  |  |  |
| 6005 | 04 | 1 | u16 |  |  |  |  |  |
| 6007 | 04 | 1 | s16 |  |  |  |  |  |
| 6450 | 04 | 1 | u16 |  |  |  |  |  |
| 6499 | 04 | 1 | u16 |  |  |  |  |  |
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
| 7890 | 04 | 1 | u16 |  |  |  |  |  |
| 7816 | 04 | 2 | u32 |  |  |  |  |  |
| 7818 | 04 | 2 | u32 |  |  |  |  |  |
| 7828 | 04 | 2 | u32 |  |  |  |  |  |
| 7831 | 04 | 2 | s32 |  |  |  |  |  |
| 7919 | 04 | 1 | u16 |  |  |  |  |  |
| 7999 | 04 | 1 | s16 |  |  |  |  |  |
| 8000 | 04 | 1 | s16 |  |  |  |  |  |
| 8001 | 04 | 1 | s16 |  |  |  |  |  |
| 8002 | 04 | 1 | u16 |  |  |  |  |  |
| 8003 | 04 | 1 | u16 |  |  |  |  |  |
| 8004 | 04 | 1 | u16 |  |  |  |  |  |
| 8005 | 04 | 2 | s32 |  |  |  |  |  |
| 8007 | 04 | 2 | s32 |  |  |  |  |  |
| 8009 | 04 | 2 | s32 |  |  |  |  |  |
| 8013 | 04 | 1 | u16 |  |  |  |  |  |
| 8036 | 04 | 1 | u16 |  |  |  |  |  |
| 8037 | 04 | 1 | u16 |  |  |  |  |  |
| 8038 | 04 | 2 | u32 |  |  |  |  |  |
| 8040 | 04 | 1 | u16 |  |  |  |  |  |
| 8041 | 04 | 1 | u16 |  |  |  |  |  |
| 8047 | 04 | 1 | u16 |  |  |  |  |  |
| 8048 | 04 | 1 | u16 |  |  |  |  |  |
| 8049 | 04 | 1 | u16 |  |  |  |  |  |
| 8050 | 04 | 1 | u16 |  |  |  |  |  |
| 8150 | 04 | 1 | u16 |  |  |  |  |  |
| 8151 | 04 | 1 | u16 |  |  |  |  |  |
| 8152 | 04 | 1 | u16 |  |  |  |  |  |
| 8153 | 04 | 1 | u16 |  |  |  |  |  |
| 8154 | 04 | 2 | s32 |  |  |  |  |  |
| 8156 | 04 | 2 | s32 |  |  |  |  |  |
| 8158 | 04 | 2 | u32 |  |  |  |  |  |
| 8160 | 04 | 1 | u16 |  |  |  |  |  |
| 8173 | 04 | 2 | s32 |  |  |  |  |  |
| 8175 | 04 | 2 | s32 |  |  |  |  |  |
| 8177 | 04 | 2 | s32 |  |  |  |  |  |
| 10451 | 04 | 1 | u16 |  |  |  |  |  |
| 10452 | 04 | 1 | u16 |  |  |  |  |  |
| 10453 | 04 | 1 | u16 |  |  |  |  |  |
| 10454 | 04 | 1 | u16 |  |  |  |  |  |
| 10455 | 04 | 1 | u16 |  |  |  |  |  |
| 13000 | 04 | 1 | u16 |  |  | dev_state | Device status |  |
| 13001 | 04 | 1 | u16 |  |  |  |  |  |
| 13002 | 04 | 1 | u16 | 0.1 | kWh | today_energy | Daily PV yield |  |
| 13003 | 04 | 2 | u32 | 0.1 | kWh | total_energy | Total PV yield |  |
| 13005 | 04 | 1 | u16 | 0.1 | kWh | deliy_supplynet_yeild | Feed-in energy today (PV) |  |
| 13006 | 04 | 2 | u32 | 0.1 | kWh | total_supplynet_yeild | Total feed-in energy (PV) |  |
| 13008 | 04 | 2 | s32 | 0.001 | kW | supply_load_curr_power | Total active power of load |  |
| 13010 | 04 | 2 | s32 |  |  |  |  |  |
| 13012 | 04 | 1 | u16 | 0.1 | kWh | battery_daliy_pv | PV-to-battery charge today |  |
| 13013 | 04 | 2 | u32 | 0.1 | kWh | battery_tot_pv | Total PV-to-battery charge |  |
| 13017 | 04 | 1 | u16 |  |  |  |  |  |
| 13018 | 04 | 2 | u32 | 0.1 | kWh | tot_directconsume_power | Total PV energy consumed by loads |  |
| 13020 | 04 | 1 | u16 | 0.1 | V | batt_voltage | Battery voltage |  |
| 13021 | 04 | 1 | u16 | 0.1 | A | batt_current | Battery current |  |
| 13022 | 04 | 1 | u16 |  | W | battery_power | Battery power |  |
| 13023 | 04 | 1 | u16 | 0.1 | % | batt_power | Battery level (SOC) |  |
| 13024 | 04 | 1 | u16 | 0.1 | % | batt_heathly | Battery SOH |  |
| 13025 | 04 | 1 | s16 | 0.1 | ℃ | batt_temp | Battery temperature |  |
| 13026 | 04 | 1 | u16 | 0.1 | kWh | battdaliy_discharge_yeild | Battery discharge today |  |
| 13027 | 04 | 2 | u32 | 0.1 | kWh | battdaliy_discharge_tot | Total battery discharge |  |
| 13029 | 04 | 1 | u16 | 0.1 | % | deliy_self_rate | Daily self-consumption rate |  |
| 13030 | 04 | 1 | u16 |  |  |  |  |  |
| 13031 | 04 | 1 | s16 | 0.1 | A | phase_current_A | Phase A current |  |
| 13032 | 04 | 1 | s16 | 0.1 | A | phase_current_B | Phase B current |  |
| 13033 | 04 | 1 | s16 | 0.1 | A | phase_current_C | Phase C current |  |
| 13034 | 04 | 2 | s32 | 0.001 | kW | curr_power | Total active power |  |
| 13036 | 04 | 1 | u16 | 0.1 | kWh | network_yeild | Energy purchased today |  |
| 13037 | 04 | 2 | u32 | 0.1 | kWh | network_total | Total purchased energy |  |
| 13039 | 04 | 1 | u16 |  |  |  |  |  |
| 13040 | 04 | 1 | u16 | 0.1 | kWh | battdaliy_charge_yeild | Battery charge today |  |
| 13041 | 04 | 2 | u32 | 0.1 | kWh | battdtot_charge_yeild | Total battery charge |  |
| 13043 | 04 | 1 | u16 |  |  |  |  |  |
| 13045 | 04 | 1 | u16 |  |  |  |  |  |
| 13046 | 04 | 2 | u32 | 0.1 | kWh | supplynet_total | Total feed-in energy |  |
| 13050 | 04 | 2 | u32 |  |  |  |  |  |
| 13052 | 04 | 2 | u32 |  |  |  |  |  |
| 13054 | 04 | 2 | u32 |  |  |  |  |  |
| 13056 | 04 | 2 | u32 |  |  |  |  |  |
| 13058 | 04 | 2 | u32 |  |  |  |  |  |
| 13060 | 04 | 2 | u32 |  |  |  |  |  |
| 13062 | 04 | 2 | u32 |  |  |  |  |  |
| 13064 | 04 | 2 | u32 |  |  |  |  |  |
| 13066 | 04 | 2 | u32 |  |  |  |  |  |
| 13068 | 04 | 2 | u32 |  |  |  |  |  |
| 13070 | 04 | 2 | u32 |  |  |  |  |  |
| 13072 | 04 | 2 | u32 |  |  |  |  |  |
| 13074 | 04 | 2 | u32 |  |  |  |  |  |
| 13076 | 04 | 2 | u32 |  |  |  |  |  |
| 13078 | 04 | 2 | u32 |  |  |  |  |  |
| 13101 | 04 | 1 | u16 |  | A | bms_charge_current | Max. charging current (BMS) |  |
| 13102 | 04 | 1 | u16 |  | A | bms_discharge_current | Max. discharging current (BMS) |  |
| 13103 | 04 | 1 | u16 |  |  |  |  |  |
| 13104 | 04 | 1 | u16 |  |  |  |  |  |
| 13105 | 04 | 1 | u16 |  |  |  |  |  |
| 13106 | 04 | 1 | u16 |  |  |  |  |  |
| 13107 | 04 | 1 | u16 |  |  |  |  |  |
| 13108 | 04 | 1 | u16 |  |  |  |  |  |
| 13109 | 04 | 1 | u16 |  |  |  |  |  |
| 13110 | 04 | 1 | u16 |  |  |  |  |  |
| 13111 | 04 | 1 | u16 |  |  |  |  |  |
| 13112 | 04 | 1 | u16 |  |  |  |  |  |
| 13113 | 04 | 1 | u16 |  |  |  |  |  |
| 13114 | 04 | 1 | u16 |  |  |  |  |  |
| 13115 | 04 | 1 | u16 |  |  |  |  |  |
| 13116 | 04 | 1 | s16 |  |  |  |  |  |
| 13117 | 04 | 1 | s16 |  |  |  |  |  |
| 13118 | 04 | 1 | s16 |  |  |  |  |  |
| 13200 | 04 | 2 | s32 |  |  |  |  |  |
| 13202 | 04 | 2 | s32 |  |  |  |  |  |
| 13204 | 04 | 2 | s32 |  |  |  |  |  |
| 13206 | 04 | 2 | s32 |  |  |  |  |  |
| 13250 | 04 | 15 | str |  |  |  |  |  |
| 13265 | 04 | 15 | str |  |  |  |  |  |
| 13280 | 04 | 15 | str |  |  |  |  |  |
| 13295 | 04 | 15 | str |  |  |  |  |  |
| 13310 | 04 | 15 | str |  |  |  |  |  |
| 13325 | 04 | 15 | str |  |  |  |  |  |
| 13340 | 04 | 15 | str |  |  |  |  |  |
| 13355 | 04 | 15 | str |  |  |  |  |  |
| 33031 | 03 | 1 | u16 |  |  |  |  |  |
| 33145 | 03 | 1 | u16 |  |  |  |  |  |
| 33002 | 03 | 1 | u16 |  |  |  |  |  |
| 33541 | 03 | 1 | u16 |  |  |  |  |  |
| 33542 | 03 | 1 | u16 |  |  |  |  |  |
| 33543 | 03 | 2 | u32 |  |  |  |  |  |
| 33545 | 03 | 1 | u16 |  |  |  |  |  |
| 33546 | 03 | 1 | u32 |  |  |  |  |  |
| 33548 | 03 | 1 | u32 |  |  |  |  |  |
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
| 32303 | 03 | 1 | u16 |  |  |  |  |  |
| 32304 | 03 | 1 | u16 |  |  |  |  |  |
| 32305 | 03 | 1 | u16 |  |  |  |  |  |
| 32306 | 03 | 1 | u16 |  |  |  |  |  |
| 32907 | 03 | 1 | u16 |  |  |  |  | capability 35.10 |
| 32909 | 03 | 1 | u16 |  |  |  |  |  |
| 32910 | 03 | 1 | u16 |  |  |  |  |  |
| 34300 | 03 | 32 | str |  |  |  |  |  |
| 34332 | 03 | 10 | str |  |  |  |  |  |
| 34342 | 03 | 1 | u16 |  |  |  |  |  |
| 34343 | 03 | 8 | str |  |  |  |  |  |
| 34351 | 03 | 15 | str |  |  |  |  |  |
| 34366 | 03 | 15 | str |  |  |  |  |  |
| 34382 | 03 | 1 | u16 |  |  |  |  |  |
| 33509 | 03 | 1 | u16 |  |  |  |  |  |
| 33050 | 03 | 1 | u16 |  |  |  |  |  |
| 33032 | 03 | 1 | u16 |  |  |  |  |  |
| 33050 | 03 | 1 | u16 |  |  |  |  |  |
| 33146 | 03 | 1 | u16 |  |  |  |  |  |
| 33147 | 03 | 1 | u16 |  |  |  |  |  |
| 33148 | 03 | 1 | u16 |  |  |  |  |  |
| 33208 | 03 | 1 | u16 |  |  |  |  |  |
| 33283 | 03 | 1 | u16 |  |  |  |  |  |
| 33284 | 03 | 1 | u16 |  |  |  |  |  |
| 33285 | 03 | 1 | u16 |  |  |  |  |  |
| 33286 | 03 | 1 | u16 |  |  |  |  |  |
| 33287 | 03 | 1 | u16 |  |  |  |  |  |
| 33288 | 03 | 1 | u16 |  |  |  |  |  |
| 34781 | 03 | 1 | u16 |  |  |  |  |  |
| 34782 | 03 | 1 | u16 |  |  |  |  |  |
| 65000 | 04 | 1 | u16 |  |  |  |  |  |
| 65001 | 04 | 1 | u16 |  |  |  |  |  |
| 65002 | 04 | 1 | u16 |  |  |  |  |  |
| 65003 | 04 | 1 | u16 |  |  |  |  |  |
