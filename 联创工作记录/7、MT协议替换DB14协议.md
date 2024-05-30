
| 煤矿名称 | 上报         |
| ---- | ---------- |
| 后安   | 省煤监        |
| 担水沟  | 省煤监        |
| 东坡   | 省煤监        |
| 何家堡  | 省煤监、怀仁市应急局 |
| 吴家窑  | 省煤监、怀仁市应急局 |
| 焦煤矿  | 省煤监、怀仁市应急局 |
| 芦子沟  |            |
| 峙峰山  |            |
| 小峪   |            |
| 王坪   |            |
| 虎龙沟  |            |
| 柴沟   |            |
| 砂石矿  |            |

安全监控

| DB14 | 文件名                                                         | 文件示例                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |     |
| :--- | :---------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| AQCS | <span style="color: rgb(209, 209, 209);">安全监控系统初始化文件</span> | ```<br><?xml version="1.0" encoding="utf-8"?><root code="DB14/T 671.1-2012"><head cs_mine_code="067000002"  cs_data_time="2024-05-26 00:30:00" /><data ss_system_model="KJ90X" ss_system_name="煤矿安全监控系统" ss_system_maker="中煤科工集团重庆研究院有限公司" ss_system_prodate="2020-03-01" ss_system_manum="MFC140166" ss_system_maeffec="2028-01-12" ss_system_instime="2020-06-22"/></root><br>```                                                                                                                                                                                            |     |
| AQKJ | 安全监控系统空间数据文件                                                | ```<br><?xml version="1.0" encoding="utf-8"?><root code="DB14/T 671.1-2012"><head cs_mine_code="067000002"  cs_data_time="2024-05-25 08:30:36" /><data ss_gisnode_code="1" cs_y_coor="0" cs_x_coor="0" cs_z_coor="0"/></root><br>```                                                                                                                                                                                                                                                                                                                                           |     |
| AQMC | 模拟量传感器文件                                                    | ```<br><?xml version="1.0" encoding="utf-8"?><root code="DB14/T 671.1-2012"><head cs_mine_code="067000001"  cs_data_time="2024-03-31 17:41:18" /><data ss_station_code="000001" ss_transducer_code="001A01" ss_transducer_type="M0104" ss_transducer_name="一氧化碳" ss_analog_unit="ppm" ss_transducer_point="主斜井皮带头一氧" ss_gisnode_code="1" ss_analog_lower="0" ss_analog_high="1000" ss_analog_alarmlow="0.00" ss_analog_alarmhigh="24.00" ss_analog_pofflow="0.00" ss_analog_poffhigh="0.00" ss_analog_ponlow="0.00" ss_analog_ponhigh="23.50" ss_poffarea_code="0000"/><br>``` |     |
| AQKC | 开关量传感器文件                                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |     |
