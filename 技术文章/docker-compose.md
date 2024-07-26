
微服务架构中，包含多个服务，每个服务又有若干实例。如果使用dockerfile构建好的镜像一个个运行将会很麻烦。可以考虑通过 docker-compose 来实现，它可以轻松的将多个容器作为 service 来运行（当然也可仅运行其中的某个），并且提供了 scale (服务扩容) 的功能



```yaml
version: '2'

services:

gateway:

image: ${GATEWAY_SERVICES_IMAGE_NAME}:${GATEWAY_SERVERS_IMAGE_TAG}

restart: always

container_name: gateway

hostname: gateway

network_mode: default

ports:

- 30181:8080

volumes:

- /home/lantrack/cdeslogs/gateway:/cdeslogs

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- nacos

sys:

image: ${SYS_SERVICES_IMAGE_NAME}:${SYS_SERVERS_IMAGE_TAG}

restart: always

container_name: sys

hostname: sys

network_mode: default

ports:

- 30182:8080

volumes:

- /home/lantrack/cdes/sys:/upload

- /home/lantrack/cdeslogs/sys:/cdeslogs

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- mysql

- tdengine

- redis

- nacos

item:

image: ${ITEM_SERVICES_IMAGE_NAME}:${ITEM_SERVERS_IMAGE_TAG}

restart: always

container_name: item

hostname: item

network_mode: default

ports:

- 30183:8080

volumes:

- /home/lantrack/cdes/item:/upload

- /home/lantrack/cdeslogs/item:/cdeslogs

- /home/lantrack/sftp/upload:/sftp

- /home/lantrack/ftp:/ftp

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- mysql

- tdengine

- redis

- nacos

ledger:

image: ${LEDGER_SERVICES_IMAGE_NAME}:${LEDGER_SERVERS_IMAGE_TAG}

restart: always

container_name: ledger

hostname: ledger

network_mode: default

ports:

- 30184:8080

volumes:

- /home/lantrack/cdes/ledger:/upload

- /home/lantrack/cdeslogs/ledger:/cdeslogs

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- mysql

- tdengine

- redis

- nacos

dataxweb:

image: ${DATAXWEB_SERVICES_IMAGE_NAME}:${DATAXWEB_SERVERS_IMAGE_TAG}

restart: always

container_name: dataxweb

hostname: dataxweb

network_mode: default

ports:

- 30185:8080

volumes:

- /home/lantrack/cdes:/home/lantrack/datax

- /home/lantrack/cdeslogs/datax:/cdeslogs

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- mysql

- tdengine

- redis

- nacos

report:

image: ${REPORT_SERVICES_IMAGE_NAME}:${REPORT_SERVERS_IMAGE_TAG}

restart: always

container_name: report

hostname: report

network_mode: default

ports:

- 30186:8080

volumes:

- /home/lantrack/cdes/report:/upload

- /home/lantrack/cdeslogs/report:/cdeslogs

- /home/lantrack/sftp/upload:/sftp

- /home/lantrack/ftp:/ftp

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- mysql

- tdengine

- redis

- nacos

video:

image: ${VIDEO_SERVICES_IMAGE_NAME}:${VIDEO_SERVERS_IMAGE_TAG}

restart: always

container_name: video

hostname: video

network_mode: default

ports:

- 30187:8080

volumes:

- /home/lantrack/cdes/video:/upload

- /home/lantrack/cdeslogs/video:/cdeslogs

extra_hosts:

- "sqh1:172.16.66.205"

- "sqh2:172.16.66.206"

- "sqh3:172.16.66.207"

- "sqh4:172.16.66.208"

- "sqh5:172.16.66.209"

external_links:

- mysql

- tdengine

- redis

- nacos

nginx:

image: nginx:1.17.9

restart: always

container_name: nginx

hostname: nginx

network_mode: default

ports:

- 30180:30180

volumes:

- /home/lantrack/nginx/html/conf:/etc/nginx/conf.d/

- /home/lantrack/nginx/html:/etc/nginx/html

environment:

TZ: Asia/Shanghai

```