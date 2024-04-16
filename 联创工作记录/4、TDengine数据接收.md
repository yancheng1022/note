
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

create stable godboot_cisp.aq_gas_hastory(ts timestamp,  sensor_value nchar(8), sensor_status nchar(8), file_update_time nchar(32)) tags (mine_code nchar(32), sensor_code nchar(64));