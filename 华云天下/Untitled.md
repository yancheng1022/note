ALTER TABLE workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';