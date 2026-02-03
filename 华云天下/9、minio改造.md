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
