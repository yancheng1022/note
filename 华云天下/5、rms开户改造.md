/RegisterEntCca 开通cca企业

1、删除：destroySQL.ftl 
2、删除：generateWorkflowSQL.ftl
3、创建初始化CCA：generateBmsSQL.ftl
4、创建和初始化工单表：generateWorkflowSQL.ftl
5、创建和初始化知识图谱：generateGraphSQL.ftl


/addEnt  gls开户

1、创建表：addEntOnly.ftl
2、创建存储过程：addEntOnly.ftl
3、初始化数据：addEntOnly.ftl


| 字段名称                    | 字段类型         | 说明       | 备注                                |
| ----------------------- | ------------ | -------- | --------------------------------- |
| id                      | bigint(20)   | 主键 ID    | 唯一标识                              |
| template_code           | varchar(64)  | 模板编码     | 唯一，如 WF_TIMELINESS_CREATE         |
| template_name           | varchar(128) | 模板名称     | 如 “流程时效表 - 创建”                    |
| sql_type                | tinyint(2)   | SQL 类型   | 1 - 创建表、2 - 删除表、3 - 初始化数据         |
| table_scope             | tinyint(2)   | 表范围      | 1 - 租户专属表（含 ${entId} 占位符）、2 - 公共表 |
| sql_content             | longtext     | SQL 模板内容 | 保留原 SQL 逻辑，含 ${entId} 占位符         |
| version                 | varchar(32)  | 模板版本     | 如 1.0.0，支持版本迭代与回滚                 |
| status                  | tinyint(2)   | 状态       | 0 - 禁用、1 - 启用（仅启用模板生效）            |
| remark                  | varchar(512) | 备注       | 记录修改原因，如 “2025-11-24 新增超时字段”      |
| create_time/update_time | datetime     | 时间戳      | 创建 / 更新时间                         |
| create_user/update_user | varchar(64)  | 操作人      | 记录操作账号                            |
