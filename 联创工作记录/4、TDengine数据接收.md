
# 1、语句备份
create stable godboot_cisp.aq_gas(
	ts timestamp, 
	sensor_value nchar(8), 
	sensor_status nchar(8), 
	file_update_time nchar(32)
) tags (
	mine_code nchar(32), 
	sensor_code nchar(64)
);


insert into godboot_cisp.aq_gas_067000002_001A03 USING godboot_cisp.aq_gas tags ('067000002', '001A03') values ('2024-04-16 13:14:04.777', '18.60', '0','2024-04-16 09:06:13');


select * from  godboot_cisp.aq_gas_067000002_013A05 where ts > '2024-04-17 09:00:00.000';

select distinct mine_code from  godboot_cisp.aq_gas;

select * from  godboot_cisp.aq_gas where mine_code = '028000001' order by ts asc limit 10;

aq_gas_028000001_029a05
# 2、同步进程

芦子沟：067000001
何家堡：067000002
吴家窑：066000002
后安：135000001
焦煤矿：002030011
140 624 000 03



# 3、数据备份
docker exec -it e16 bash

select * from aq_gas_067000002_200106 where ts> '2024-12-15 03:57:00.000' >> data.csv;

docker cp <containerId>:/path/to/file/in/container /path/to/destination/on/host
docker cp /path/to/file/on/host <containerId>:/path/to/destination/in/container