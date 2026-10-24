# RS-485 poll map: battery stack (device type 44, read through the inverter)

Registers the dongle reads for each battery stack. The inverter answers these on the stack's slave address from its CAN link to the battery; the battery itself has no RS-485 connection to the dongle.

Addresses are 1-based, as in Sungrow's published Modbus documentation (subtract 1 for the value sent on the wire). `fn` is the Modbus function code in hex. `field` is the dongle's short name for the value; `label` is the English name the web interface shows. Rows without a field or label are polled but not displayed. 18 of 115 rows are named.

| address | fn | count | type | scale | unit | field | label | poll | notes |
|---|---|---|---|---|---|---|---|---|---|
| 5448 | 04 | 25 | str |  |  |  |  | every pass |  |
| 10701 | 04 | 4 | str |  |  |  |  | every pass |  |
| 10705 | 04 | 4 | str |  |  |  |  | every pass |  |
| 10628 | 04 | 1 | s16 |  |  |  |  | every pass |  |
| 10629 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 10630 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 10631 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 10632 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 10633 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 10635 | 04 | 15 | str |  |  |  |  | every pass | capability 1.3 |
| 10650 | 04 | 2 | s32 |  |  |  |  | every pass | capability 1.3 |
| 10652 | 04 | 2 | s32 |  |  |  |  | every pass | capability 1.3 |
| 10654 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.3 |
| 10655 | 04 | 2 | s32 |  |  |  |  | every pass | capability 1.3 |
| 10657 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.3 |
| 10658 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.3 |
| 10659 | 04 | 2 | s32 |  |  |  |  | every pass | capability 1.3 |
| 10663 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.4 |
| 10666 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10667 | 04 | 8 | str |  |  |  |  | every pass | capability 1.1 |
| 10675 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10676 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10677 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10678 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10679 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10680 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10681 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10682 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10683 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10684 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10685 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.2 |
| 10687 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10688 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.2 |
| 10690 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10691 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.2 |
| 10693 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10694 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10695 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10696 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10697 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10698 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.2 |
| 10699 | 04 | 2 | u32 |  |  |  |  | every pass |  |
| 10709 | 04 | 1 | u16 |  |  |  |  | every pass |  |
| 10710 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.0 |
| 10711 | 04 | 10 | str |  |  | dev_sn | Device S/N | every pass | capability 1.0 |
| 10721 | 04 | 15 | str |  |  | battery_version | Current version | every pass | capability 1.0 |
| 10736 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.0 |
| 10738 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.0 |
| 10740 | 04 | 1 | u16 |  |  | batt_operation_status | Battery operating status | every pass | capability 1.0 |
| 10741 | 04 | 1 | u16 | 0.1 | V | batt_voltage | Battery voltage | every pass | capability 1.0 |
| 10742 | 04 | 1 | s16 | 0.1 | A | batt_current | Battery current | every pass | capability 1.0 |
| 10743 | 04 | 1 | s16 | 0.1 | ℃ | batt_temp | Battery temperature | every pass | capability 1.0 |
| 10744 | 04 | 1 | u16 | 0.1 | % | batt_remain_power | Battery SOC | every pass | capability 1.0 |
| 10745 | 04 | 1 | u16 | 1 | % | batt_heathly | Battery SOH | every pass | capability 1.0 |
| 10746 | 04 | 2 | u32 | 0.1 | kWh | battdtot_charge_yeild | Total battery charge | every pass | capability 1.0 |
| 10748 | 04 | 2 | u32 | 0.1 | kWh | totbms_discharge_yeild | Total battery discharge (BMS) | every pass | capability 1.0 |
| 10750 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.0 |
| 10751 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.0 |
| 10757 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10758 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10759 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10760 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10761 | 04 | 1 | s16 |  |  |  |  | every pass | capability 1.1 |
| 10762 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10763 | 04 | 1 | s16 |  |  |  |  | every pass | capability 1.1 |
| 10764 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10765 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10766 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10767 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10768 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10769 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10770 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10771 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10772 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10773 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10774 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10775 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10776 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10777 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10778 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10779 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10780 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10781 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10782 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10783 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10784 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10785 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10786 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10787 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10788 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10789 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10790 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10791 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10793 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10795 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10797 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10799 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10801 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10803 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10805 | 04 | 2 | u32 |  |  |  |  | every pass | capability 1.1 |
| 10811 | 04 | 2 | u32 |  |  |  |  | every pass |  |
| 10813 | 04 | 2 | u32 |  |  |  |  | every pass |  |
| 10815 | 04 | 2 | u32 |  |  |  |  | every pass |  |
| 10817 | 04 | 2 | u32 |  |  |  |  | every pass |  |
| 10819 | 04 | 2 | u32 |  |  |  |  | every pass |  |
| 10821 | 04 | 1 | u16 |  |  |  |  | every pass | capability 1.1 |
| 10822 | 04 | 9 | str |  |  | batt_pack1_sn | Pack 1 S/N | every pass | capability 1.1 |
| 10831 | 04 | 9 | str |  |  | batt_pack2_sn | Pack 2 S/N | every pass | capability 1.1 |
| 10840 | 04 | 9 | str |  |  | batt_pack3_sn | Pack 3 S/N | every pass | capability 1.1 |
| 10849 | 04 | 9 | str |  |  | batt_pack4_sn | Pack 4 S/N | every pass | capability 1.1 |
| 10858 | 04 | 9 | str |  |  | batt_pack5_sn | Pack 5 S/N | every pass | capability 1.1 |
| 10867 | 04 | 9 | str |  |  | batt_pack6_sn | Pack 6 S/N | every pass | capability 1.1 |
| 10876 | 04 | 9 | str |  |  | batt_pack7_sn | Pack 7 S/N | every pass | capability 1.1 |
| 10885 | 04 | 9 | str |  |  | batt_pack8_sn | Pack 8 S/N | every pass | capability 1.1 |
| 19939 | 04 | 2 | u32 |  |  |  |  | every pass |  |
