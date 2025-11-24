
ublic static final String API_CALL_LOG_WITH_TASK = "api_call_log_with_task";


package com.huayunworld.cca.shortmessage.service.impl;  
  
import com.alibaba.cloud.commons.lang.StringUtils;  
import com.alibaba.fastjson.JSON;  
import com.alibaba.fastjson.JSONObject;  
import com.cncloud.qframe.exception.ServiceException;  
import com.huayunworld.cca.common.constant.Constants;  
import com.huayunworld.cca.common.util.MessageUtils;  
import com.huayunworld.cca.common.util.ObjectUtils;  
import com.huayunworld.cca.common.util.SendMessageUtil;  
import com.huayunworld.cca.shortmessage.constant.MQConstants;  
import com.huayunworld.cca.shortmessage.service.ISendMessageTypeService;  
import org.apache.activemq.command.ActiveMQTopic;  
import org.apache.commons.codec.digest.DigestUtils;  
import org.apache.http.Header;  
import org.apache.http.client.methods.CloseableHttpResponse;  
import org.apache.http.client.methods.HttpPost;  
import org.apache.http.entity.StringEntity;  
import org.apache.http.impl.client.CloseableHttpClient;  
import org.apache.http.impl.client.HttpClients;  
import org.apache.http.util.EntityUtils;  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
import org.springframework.jms.core.JmsTemplate;  
import org.springframework.stereotype.Service;  
  
import javax.annotation.Resource;  
import javax.jms.Destination;  
import java.io.IOException;  
import java.util.*;  
  
/**  
 * 中燃短信网关 Service  
 */@Service("SendMessageZRQServiceImpl")  
public class SendMessageZRQServiceImpl implements ISendMessageTypeService {  
  
    private static Logger log = LoggerFactory.getLogger(SendMessageZRQServiceImpl.class);  
  
    @Resource  
    private JmsTemplate jmsTemplate;  
  
    @Override  
    public String sendMessage(Map<String, String> map) throws ServiceException {  
        log.info("进入发送短信方法 SendMessageZRQServiceImpl.sendMessage(),接收到的参数为：{}", map);  
        JSONObject resultJson = new JSONObject();  
        String sendStatus;  
        String sendMsg;  
        CloseableHttpClient client = HttpClients.createDefault();  
        CloseableHttpResponse response = null;  
        String url = map.get("url");  
        JSONObject logObject = new JSONObject();  
        HttpPost httpPost = new HttpPost(url);  
        JSONObject param = new JSONObject();  
        Header[] responseHeaders = null;  
        String code = "";  
        String result = "";  
        Long startTime = 0L;  
        Long durationMs = 0L;  
        try {  
            //手机号码  
            String mobile = map.get("mobile");  
            if (SendMessageUtil.isCellPhone(mobile)) {  
                //短信内容  
                String message = map.get("message");  
                //短信网关  
                JSONObject jsonData = JSON.parseObject(map.get("param"));  
                //这里是参数列表  
                String appId = jsonData.getString("accesskey");  
                String appSecret = jsonData.getString("secret");  
                String timestamp = String.valueOf(System.currentTimeMillis());  
                String data = appId + timestamp + appSecret;  
                String sign = DigestUtils.md5Hex(data).toUpperCase();  
                httpPost.setHeader("X-APP-ID", appId);  
                httpPost.setHeader("X-TIMESTAMP", timestamp);  
                httpPost.setHeader("X-SIGN", sign);  
                List<String> phoneList = new ArrayList<>();  
                phoneList.add(mobile);  
                param.put("content", message);  
                param.put("phoneNumberList", phoneList);  
                StringEntity entity = new StringEntity(param.toString(), "UTF-8");  
                entity.setContentType("application/json");  
                httpPost.setEntity(entity);  
                startTime = System.currentTimeMillis();  
                response = client.execute(httpPost);  
                durationMs = System.currentTimeMillis() - startTime;  
                log.info("result:[{}]", response);  
                if (!ObjectUtils.empty(response)) {  
                    responseHeaders = response.getAllHeaders();  
                    result = EntityUtils.toString(response.getEntity(), "UTF-8");  
                    if (result.startsWith("{")) {  
                        JSONObject rs = JSONObject.parseObject(result);  
                        code = rs.getString("code");  
                        if ("200".equals(code)) {  
                            sendStatus = Constants.SUCCESS;  
                            sendMsg = "发送成功";  
                            log.error(result);  
                        } else {  
                            sendStatus = Constants.SEND_FAILURE;  
                            sendMsg = rs.get("message") != null ? rs.get("message").toString() : result;  
                        }  
                    } else {  
                        sendStatus = Constants.SEND_FAILURE;  
                        sendMsg = result;  
                    }  
                } else {  
                    sendStatus = Constants.SEND_FAILURE;  
                    sendMsg = "接口未返回数据";  
                }  
            } else {  
                sendStatus = Constants.SEND_FAILURE;  
                sendMsg = MessageUtils.message("sms.phone.error");  
            }  
        } catch (Exception e) {  
            log.error("发送短信异常 SendMessageZRQServiceImpl.sendMessage()", e);  
            sendStatus = Constants.SEND_FAILURE;  
            sendMsg = MessageUtils.message("sms.exception");  
        }  
        resultJson.put("sendStatus", sendStatus);  
        resultJson.put("sendMsg", sendMsg);  
        try {  
            logObject.put("callType", "API");  
            logObject.put("status", Constants.SUCCESS.equals(sendStatus) ? "SUCCESS" : "FAILURE");  
            logObject.put("requestUrl", url);  
            logObject.put("requestMethod", "POST");  
            Header[] allHeaders = httpPost.getAllHeaders();  
            JSONObject headObject = new JSONObject();  
            Arrays.stream(allHeaders).forEach(header -> headObject.put(header.getName(), header.getValue()));  
            logObject.put("requestHeaders", headObject.toJSONString());  
            logObject.put("requestBody", param.toString());  
            logObject.put("requestTime", startTime);  
            logObject.put("responseHttpStatus", code);  
            if (responseHeaders != null){  
                JSONObject responseHeadObject = new JSONObject();  
                Arrays.stream(responseHeaders).forEach(header -> responseHeadObject.put(header.getName(), header.getValue()));  
                logObject.put("responseHeaders", responseHeadObject.toJSONString());  
            }  
            logObject.put("responseBody", result);  
            logObject.put("errorMessage", sendMsg);  
            logObject.put("durationMs", durationMs);  
            logObject.put("callerIdentifier", "shortmessage:SendMessageZRQServiceImpl");  
            logObject.put("apiName", "中燃短信发送接口");  
            Long time = System.currentTimeMillis();  
            logObject.put("createTime", time);  
            logObject.put("updateTime", time);  
            logObject.put("recordTime", time);  
            String topicName = MQConstants.API_CALL_LOG_WITH_TASK;  
            Destination topicDestination = new ActiveMQTopic(topicName);  
            log.info("开始推送中燃短信发送日志TOPIC消息 SendMessageZRQServiceImpl.sendMessage()，推送的数据为：topicName={},content={}",topicName, logObject);  
            jmsTemplate.convertAndSend(topicDestination, logObject.toJSONString());  
            log.info("推送中燃短信发送日志TOPIC消息成功");  
        } catch (Exception e) {  
            log.error("中燃短信发送日志TOPIC消息消息异常", e);  
        }  
        return resultJson.toJSONString();  
    }  
  
  
    @Override  
    public String sendMessageGroup(Map<String, String> map) throws ServiceException {  
        return sendMessage(map);  
    }  
  
  
    public static void main(String[] args) {  
        String validCode = "1234";  
        String telephone = "13672161182";  
        int minute = 5;  
        JSONObject param = new JSONObject();  
        String content = "【中国燃气】您的短信验证码为" + validCode + "，该验证码在" + minute + "分钟内有效，请您尽快完成操作（中燃集团–举报中心）";  
        List<String> phoneList = new ArrayList<String>();  
        phoneList.add(telephone);  
        param.put("content", content);  
        param.put("phoneNumberList", phoneList);  
        JSONObject result = doPostSendSMSNew(param);  
        System.out.println(result);  
    }  
  
  
    public static JSONObject doPostSendSMSNew(JSONObject param) {  
        CloseableHttpResponse response = null;  
        JSONObject returnObj = new JSONObject();  
        String url = "https://cjdsm.chinagasholdings.com/api/open-api/v2/text/task/create";  
        String appId = "c2k26U8XBSly7hh4BZdzXXjk1wkGKMgQ";  
        String appSecret = "Ptsty0BPsjJckwQOyW2eOJP0W85EQYRDfpsJ6SSHvBXGB6AhKNplXmD4gLM7Q5KY";  
        CloseableHttpClient client = HttpClients.createDefault();  
        try {  
            if (StringUtils.isBlank(url)) {  
                returnObj.put("success", "false");  
                returnObj.put("msg", "无平台地址");  
                return returnObj;  
            }  
            HttpPost httpPost = new HttpPost(url);  
            String timestamp = String.valueOf(System.currentTimeMillis());  
            String data = appId + timestamp + appSecret;  
            String sign = DigestUtils.md5Hex(data).toUpperCase();  
            httpPost.setHeader("X-APP-ID", appId);  
            httpPost.setHeader("X-TIMESTAMP", timestamp);  
            httpPost.setHeader("X-SIGN", sign);  
            StringEntity entity = new StringEntity(param.toString(), "UTF-8");  
            entity.setContentType("application/json");  
            httpPost.setEntity(entity);  
            response = client.execute(httpPost);  
            if (response.getEntity() != null) {  
                String result = EntityUtils.toString(response.getEntity(), "UTF-8");  
                if (result.startsWith("{")) {  
                    JSONObject rs = JSONObject.parseObject(result);  
                    String code = rs.getString("code");  
                    if ("200".equals(code)) {  
                        returnObj.put("success", "true");  
                        returnObj.put("msg", rs.get("message"));  
                        log.error(result);  
                    } else {//403,短信发送失败  
                        returnObj.put("success", "false");  
                        returnObj.put("msg", rs.get("message"));  
                        log.error(result);  
                    }  
                } else {  
                    returnObj.put("success", "false");  
                    returnObj.put("msg", result);  
                }  
            } else {  
                returnObj.put("success", "false");  
                returnObj.put("msg", "短信发送失败");  
            }  
        } catch (Exception e) {  
            log.error("发送短信出现异常:[{}]", e.getMessage());  
            returnObj.put("success", "false");  
            returnObj.put("msg", "短信发送失败");  
        } finally {  
            if (response != null) {  
                try {  
                    response.close();  
                } catch (IOException e) {  
                    log.error("httpclient===关闭流异常" + e.getMessage() + ",错误信息" + e);  
                }  
            }  
        }  
        return returnObj;  
    }  
  
  
}