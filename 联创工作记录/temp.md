

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


```java
package net.lantrack.module.alarm.consumer;  
  
import com.alibaba.fastjson.JSONObject;  
import lombok.extern.slf4j.Slf4j;  
import net.lantrack.framework.common.utils.DateUtil;  
import net.lantrack.module.alarm.entity.GasAlarmEntity;  
import net.lantrack.module.alarm.service.GasAlarmService;  
import org.apache.commons.lang.StringUtils;  
import org.apache.kafka.clients.admin.NewTopic;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.beans.factory.annotation.Value;  
import org.springframework.context.annotation.Bean;  
import org.springframework.kafka.annotation.KafkaListener;  
import org.springframework.stereotype.Component;  
  
import java.util.Objects;  
  
@Component  
@Slf4j  
public class HandleAlarmConsumer {  
  
  
    @Autowired  
    GasAlarmService gasAlarmService;  
  
    @Value("${baseInfo.mineCode:xxxx}")  
    private String mineCode;  
  
    /**  
     * springboot 2.2.1前版本无该配置：spring.kafka.listener.missing-topics-fatal = false  
     * 项目启动时无topic会报错  
     * 项目启动时自动配置对应的topic主题（有不创建，没有则自动创建）  
     */  
    @Bean  
    public NewTopic topic() {  
        return new NewTopic("gasAlarm-" + mineCode, 1, (short) 1);  
    }  
  
  
    @KafkaListener(topics = "gasAlarm-" + "#{'${baseInfo.mineCode}'}")  
    public void receiveAdminMessage(String message) {  
        log.error("开始处理4012手动结束瓦斯告警: " + message);  
        // 检查字符串是否以双引号开始和结束  
        if (StringUtils.isNotEmpty(message) && message.startsWith("\"") && message.endsWith("\"")) {  
            // 移除第一个和最后一个字符（即双引号）  
            message = message.substring(1, message.length() - 1);  
        }  
        GasAlarmEntity gasAlarmEntity = JSONObject.parseObject(message.replaceAll("\\\\", ""), GasAlarmEntity.class);  
        // 更新结束状态  
        GasAlarmEntity gasAlarm = gasAlarmService.getById(gasAlarmEntity.getId());  
        if (gasAlarm != null && gasAlarm.getAlarmEndDate() != null) {  
            GasAlarmEntity data = new GasAlarmEntity();  
            data.setId(gasAlarmEntity.getId());  
            data.setAlarmEndDate(gasAlarmEntity.getAlarmEndDate());  
            data.setRecordName(DateUtil.getDateTime());  
            gasAlarmService.updateById(data);  
            log.error("4012手动结束瓦斯告警: 处理成功");  
            return;  
        }  
        log.error("4012手动结束瓦斯告警: 已存在结束时间，处理失败");  
    }  
  
}

```