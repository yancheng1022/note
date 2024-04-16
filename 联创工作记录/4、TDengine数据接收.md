
CREATE STABLE `godboot_cisp`.`aq_gas_hastory` (
`ts` TIMESTAMP,
`mine_code` VARCHAR(32),
`sensor_code` VARCHAR(64),
`sensor_value` VARCHAR(8),
`sensor_status` VARCHAR(8),
`file_update_time` VARCHAR(32),
)
TAGS (
	`group_id` INT,
	`location` VARCHAR(24)
);