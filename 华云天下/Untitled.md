ALTER TABLE GYCZ_workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';

ALTER TABLE GYXS_workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';


ALTER TABLE `hycca_customer_4`.LT114_recent_20_reported_info

MODIFY COLUMN `field03` varchar(100) DEFAULT NULL COMMENT '预留字段03';