
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

CREATE STABLE godboot_cisp.aq_gas_history ( 
	ts TIMESTAMP, 
	mine_code BINARY(32), 
	sensor_code BINARY(64), 
	sensor_value BINARY(8), 
	sensor_status BINARY(8), 
	file_update_time BINARY(32));

CREATE STABLE godboot_cisp.aq_gas_history ( 
    ts TIMESTAMP, 
    mine_code BINARY(32), 
    sensor_code BINARY(64), 
    sensor_value BINARY(8), 
    sensor_status BINARY(8), 
    file_update_time BINARY(32)
);