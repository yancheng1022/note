# 1、环境

1、测试环境：
dev01@60.10.135.210 -p 10049
密码: dev01@#$%^

![企业微信截图_17700124309239.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_17700124309239.png)


```java

@Autowired  
private FileService fileService;

@Value("${file.storage.platform}")  
private String platform;  
@Value("${file.storage.url}")  
private String fileStorageUrl;


String url = fileStorageUrl+fileService.upload(uploadFiles, platform, ShiroUtils.getEntId());
```

# 2、改造记录

| 模块       | 功能                    | 接口                           |
| -------- | --------------------- | ---------------------------- |
| hy-email | 邮件工作台 - 发送邮件 - 正文上传图片 | /email/fileOper/uploadImage  |
| hy-email | 上传附件-通用接口             | /email/fileOper/uploadAttach |
| hy-email | 邮件接收任务                | ReceivingMailJob.execute     |
| hy-email |                       | /email//create/uploadImages  |


国银那边，年前的bug都改完了，如果有新提的话继续看
还有就是主产品hy模块minio改造，之前一直没空看，就改了几个接口，这周有空的话继续改


| 项目       | 详细信息                                                                                                                                                                                                      |
| :------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **拍摄机构** | 北遇映画（厦门红房子店）                                                                                                                                                                                              |
| **拍摄时间** | 选衣：4.28 <br>拍摄：4.29 <br>选片：4.30                                                                                                                                                                           |
| **拍摄场地** | 1、岛外外景：镇海角 <br>2、岛内外景：南湖/海湾<br>3、岛内外景：楼上天台<br>4、内景                                                                                                                                                        |
| **套餐费用** | **套餐价：** 8888元 <br>**用车费用：** 约300元 <br>**外景门票：** 待定 <br>**额外精修：** 150元/张 <br>**酒店住宿：** 自费                                                                                                                 |
| **付款方式** | **定金：** 预订支付30% <br>**尾款：** 拍摄当天早上缴清 <br>**改期政策：** 需提前15天沟通                                                                                                                                               |
| **拍摄准备** | **1. 方案沟通：** 拍摄前一周定PPT方案 <br>**2. 新娘准备：** 胸贴、无痕内裤、舒适平底鞋，确保发色统一 <br>**3. 新郎准备：** 黑白长袜，拍摄前一周修剪两鬓                                                                                                            |
| **最终交付** | **服装造型：** 4套服装，5组造型 <br>**底片：** 不少于210张 <br>**精修：** 商业精修42张 <br>**实体产品：** <br>   - 相册2本+皮袋 <br>   - 油画框30寸 x1 <br>   - 桌摆10寸 x2 <br>   - 结婚证件照 x1 <br>   - 四宫格肖像框 x1 <br>   - 80寸婚礼海报 x1 <br>   - 花絮照片15张 |
