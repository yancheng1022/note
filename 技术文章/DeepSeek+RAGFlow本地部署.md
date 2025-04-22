

1、修改ragflow项目下\ragflow\docker\.env

RAGFLOW_IMAGE=infiniflow/ragflow:v0.17.2-slim（轻量版）

RAGFLOW_IMAGE=infiniflow/ragflow:v0.17.2（完整版）
注释掉轻量版，使用完整版

开启hyper-V

docker compose -f docker-compose.yml up -d