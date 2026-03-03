ALTER TABLE GYCZ_workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';

ALTER TABLE GYXS_workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';