
CREATE STABLE `godboot_cisp`.`aq_gas_hastory` (
`ts` TIMESTAMP,
`mine_code` VARCHAR(32),
`sensor_code` VARCHAR(64),
`sensor_value` VARCHAR(8),
`sensor_status` VARCHAR(8),
`file_update_time` VARCHAR(32)
)TAGS (
	`mine_code` VARCHAR(32),
	`sensor_code` VARCHAR(64)
);

create stable godboot_cisp.aq_gas_history(
	ts timestamp, 
	sensor_value nchar(8), 
	sensor_status nchar(8), 
	file_update_time nchar(32)
) tags (
	mine_code nchar(32), 
	sensor_code nchar(64)
);


INSERT INTO godboot_cisp.aq_gas_067000002_001A03 USING aq_gas_hastory.meters TAGS ('067000002', '001A03') VALUES ('2024-04-16 13:14:04.777', '18.60', '0','2024-04-16 09:06:13');