

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

