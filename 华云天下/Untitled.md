ALTER TABLE GYCZ_workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';

ALTER TABLE GYXS_workflow_operation_record 
MODIFY COLUMN `reminder_content` TEXT DEFAULT NULL COMMENT '催单内容';


ALTER TABLE `hycca_customer_4`.LT114_recent_20_reported_info

MODIFY COLUMN `field03` varchar(100) DEFAULT NULL COMMENT '预留字段03';





[nested] nested object under path [historyData.dataVos] is not of nested type. 我的 "dataVos": { "properties": { "dataId": { "type": "keyword", "fields": { "keyword": { "type": "keyword", "ignore_above": 256 } } },不是nested类型，要改成这个类型，怎么改，线上已有数据



1 、创建索引
curl -X PUT "localhost:9200/work_order_info?pretty" -H "Content-Type: application/json" -u elastic:elastic@huayunworld.com -d'