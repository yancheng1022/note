

```shell
# 拉取新镜像
docker pull 39.105.159.2/library/2016-tto-server-prod/etl-person:tto_0.1.79
# 停止当前容器
docker stop person
# 删除容器
docker rm person
# 启动新person容器
docker run -d --name person \
> --add-host node061:200.0.0.61 \
> --add-host node062:200.0.0.62 \
> --add-host node063:200.0.0.63 \
> --add-host node064:200.0.0.64 \
> --add-host node065:200.0.0.65 \
> --add-host node066:200.0.0.66 \
> --add-host node067:200.0.0.67 \
> --add-host node061.lantrack.net:200.0.0.61 \
> --add-host node062.lantrack.net:200.0.0.62 \
> --add-host node063.lantrack.net:200.0.0.63 \
> --add-host node064.lantrack.net:200.0.0.64 \
> --add-host node065.lantrack.net:200.0.0.65 \
> --add-host node066.lantrack.net:200.0.0.66 \
> --add-host node067.lantrack.net:200.0.0.67 \
> --add-host myhbase:100.127.2.68 \
> --restart=always -p 10952:8080 -v /home/lantrack/personEtlUpload:/upload  -v /home/lantrack/tto:/conf 3ibrary/2016-tto-server-prod/person:tto_0.1.79
# 启动新gas容器
 docker run -d --name gas \
> --add-host node061:200.0.0.61 \
> --add-host node062:200.0.0.62 \
> --add-host node063:200.0.0.63 \
> --add-host node064:200.0.0.64 \
> --add-host node065:200.0.0.65 \
> --add-host node066:200.0.0.66 \
> --add-host node067:200.0.0.67 \
> --add-host node061.lantrack.net:200.0.0.61 \
> --add-host node062.lantrack.net:200.0.0.62 \
> --add-host node063.lantrack.net:200.0.0.63 \
> --add-host node064.lantrack.net:200.0.0.64 \
> --add-host node065.lantrack.net:200.0.0.65 \
> --add-host node066.lantrack.net:200.0.0.66 \
> --add-host node067.lantrack.net:200.0.0.67 \
> --add-host myhbase:100.127.2.68 \
> --restart=always -p 10951:8080 -v /home/lantrack/gasEtlUpload:/upload -v /home/lantrack/tto:/conf 39.10ry/2016-tto-server-prod/gas:tto_0.1.79

```




docker logs --since='2024-06-02T07:00:00' --until='2024-06-02T09:00:00' 5794d > /home/gas-file.txt
