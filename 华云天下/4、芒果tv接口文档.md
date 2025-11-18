# 第三方呼叫中心接口文档

## 目录

- [概述](#概述)
- [通用说明](#通用说明)
  - [认证方式](#认证方式)
  - [响应格式](#响应格式)
- [接口列表](#接口列表)
  - [1. 用户信息查询接口](#1-用户信息查询接口)
  - [2. 短信发送接口](#2-短信发送接口)
  - [3. 地址联动接口](#3-地址联动接口)
  - [4. 工单分类信息接口](#4-工单分类信息接口)
  - [5. 第三方上传获取授权接口](#5-第三方上传获取授权接口)
  - [6. 工单信息提交接口](#6-工单信息提交接口)
  - [7. 文件上传签名获取接口](#7-文件上传签名获取接口)
  - [8. 文件访问权限设置接口](#8-文件访问权限设置接口)
  - [9. 工号和分机号绑定接口](#9-工号和分机号绑定接口)
  - [10. 软电话登出接口](#10-软电话登出接口)
  - [11. 按键随路数据信息接口](#11-按键随路数据信息接口)
  - [12. 小休信息上报接口](#12-小休信息上报接口)
  - [13. 通话 ucid 信息上报接口](#13-通话-ucid-信息上报接口)
  - [14. 工单信息列表查询接口](#14-工单信息列表查询接口)
  - [15. 工单信息详情查询接口](#15-工单信息详情查询接口)
  - [16. 会员多重身份查询接口](#16-会员多重身份查询接口)
  - [17. 会员订单信息查询接口](#17-会员订单信息查询接口)
  - [18. 会员服务信息查询接口](#18-会员服务信息查询接口)
  - [19. 会员观影券查询接口](#19-会员观影券查询接口)
  - [20. 会员代金券查询接口](#20-会员代金券查询接口)
  - [21. 会员退费订单查询接口](#21-会员退费订单查询接口)
  - [22. 用户行为记录信息查询接口](#22-用户行为记录信息查询接口)
  - [23. 用户播放记录信息查询接口](#23-用户播放记录信息查询接口)
  - [24. 录音播放地址接口](#24-录音播放地址接口)
  - [25. 电话号码判断 VIP 接口](#25-电话号码判断-vip-接口)
  - [26. 短信按 code 发送接口](#26-短信按-code-发送接口)
  - [27. 满意度调查上报接口](#27-满意度调查上报接口)
  - [28. 按键随录数据上报接口](#28-按键随录数据上报接口)
  - [29. 字典列表查询接口](#29-字典列表查询接口)
  - [30. 获取组织列表接口](#30-获取组织列表接口)
  - [31. 第三方管理员登录接口](#31-第三方管理员登录接口)
  - [32. 第三方 CRM 查询工具首页接口](#32-第三方crm查询工具首页接口)
  - [33. 第三方 CRM 呼叫中心工具首页接口](#33-第三方crm呼叫中心工具首页接口)
  - [34. 第三方用户行为记录信息查询接口](#34-第三方用户行为记录信息查询接口)
  - [35. 第三方用户播放记录信息查询接口](#35-第三方用户播放记录信息查询接口)
  - [36. 第三方用户信息查询接口](#36-第三方用户信息查询接口)
  - [37. 第三方用户重置接口](#37-第三方用户重置接口)
- [错误码说明](#错误码说明)
- [注意事项](#注意事项)
- [请求示例](#请求示例)
- [状态码说明](#状态码说明)
- [数据字典](#数据字典)
- [接口调用频率限制](#接口调用频率限制)
- [安全说明](#安全说明)
- [常见问题](#常见问题)
- [技术支持](#技术支持)

## 概述

本文档描述了第三方呼叫中心系统的所有 API 接口，包括用户信息查询、短信发送、地址联动、工单管理、文件上传、坐席绑定等功能。

## 通用说明

### 认证方式

所有接口都需要通过 `@ThirdPartyFilter` 进行第三方鉴权，需要传递以下参数：

- `appid`: 第三方应用 ID
- `sign`: 签名值
- `timestamp`: 时间戳(毫秒)
- `uuid`: 用户 UUID

### 响应格式

所有接口都返回统一的 `ResponseDTO` 格式：

```json
{
  "code": 200, // 响应状态码
  "msg": "success", // 响应消息
  "data": {} // 响应数据
}
```

## 接口列表

### 1. 用户信息查询接口

**接口地址**: `POST /api/thirdparty/callcenter/userInfo`

**功能描述**: 根据电话号码查询用户信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| username | String | 是 | - | 登录名(昵称/手机号码) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "uuid": "user123456", // 用户唯一标识
    "username": "张三", // 用户名
    "nickname": "小张", // 昵称
    "mobile": "13800138000", // 手机号码
    "email": "zhangsan@example.com", // 邮箱地址
    "avatar": "http://example.com/avatar.jpg", // 头像URL
    "gender": 1, // 性别：1-男，2-女，0-未知
    "birthday": "1990-01-01", // 生日
    "registerTime": "2020-01-01 10:00:00", // 注册时间
    "lastLoginTime": "2024-01-01 15:30:00", // 最后登录时间
    "status": 1, // 用户状态：1-正常，0-禁用
    "vipLevel": 2, // VIP等级
    "points": 1000, // 积分
    "realName": "张三", // 真实姓名
    "idCard": "110101199001011234", // 身份证号
    "address": "北京市朝阳区", // 地址
    "qq": "123456789", // QQ号
    "wechat": "zhangsan123" // 微信号
  }
}
```

### 2. 短信发送接口

**接口地址**: `POST /api/thirdparty/callcenter/sendMsg`

**功能描述**: 发送短信

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| mobile | String | 是 | - | 发送手机号码 |
| msg | String | 是 | - | 发送内容 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "messageId": "msg123456789", // 短信消息ID
    "sendTime": "2024-01-01 15:30:00", // 发送时间
    "status": "SENT" // 发送状态：SENT-已发送，FAILED-发送失败
  }
}
```

### 3. 地址联动接口

**接口地址**: `POST /api/thirdparty/callcenter/address`

**功能描述**: 地址联动查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| parentId | int | 是 | 0 | 地址上级编号 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": [
    {
      "id": 110000, // 地区ID
      "name": "北京市", // 地区名称
      "parentId": 0, // 父级地区ID
      "level": 1, // 地区级别：1-省，2-市，3-区
      "code": "110000", // 地区编码
      "sort": 1, // 排序
      "status": 1 // 状态：1-启用，0-禁用
    }
  ]
}
```

### 4. 工单分类信息接口

**接口地址**: `GET /api/thirdparty/callcenter/workOrderCategory`

**功能描述**: 工单分类信息查询-一次返回所有的分类数据，前端自行处理

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| parentId | int | 是 | 0 | 上级分类 id |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": [
    {
      "categoryId": 639, // 分类ID
      "name": "页面操作闪退1", // 分类名称
      "parentId": 553 // 分级分类ID，0为顶级
    }
  ]
}
```

### 5. 第三方上传获取授权接口

**接口地址**: `GET /api/thirdparty/callcenter/uploadSign`

**功能描述**: 获取上传授权

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| type | int | 否 | 0 | 默认 0, 0 图片，1 视频 |
| num | int | 是 | 0 | 数量 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "uploadUrls": [
      {
        "key": "uploads/2024/01/01/file1.jpg",
        "url": "https://example.com/upload/url",
        "expires": 3600
      }
    ],
    "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
    "accessKeySecret": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
    "securityToken": "AQoEXAMPLEH4aoAH0gNCAPyJxz4BlCFFxWNE1OPTgk5TthT...",
    "expiration": "2024-01-01T16:30:00Z",
    "bucket": "mgtv-upload",
    "region": "cn-north-1"
  }
}
```

### 6. 工单信息提交接口

**接口地址**: `POST /api/thirdparty/callcenter/workOrder/create`

**功能描述**: 提交工单信息新增或修改

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| seatsId | String | 否 | - | 工号 |
| extensionNo | String | 否 | - | 分机号 |
| mobile | String | 是 | - | 呼叫号码 |
| ucid | String | 是 | - | 呼叫唯一编号 |
| qq | String | 否 | - | QQ 号码 |
| nickName | String | 否 | - | 用户昵称 |
| uuid | String | 否 | - | 用户 UUID |
| memberCate | String | 否 | - | 会员类型 |
| ip | String | 否 | - | 用户 IP |
| country | String | 否 | - | 国家 |
| province | String | 否 | - | 省 |
| city | String | 否 | - | 市 |
| area | String | 否 | - | 区/县 |
| street | String | 否 | - | 街道 |
| address | String | 否 | \* | 详细地址 |
| clientType | String | 是 | - | 平台类型 |
| equipmentBrand | String | 否 | - | 设备品牌 |
| equipmentModel | String | 否 | - | 设备型号 |
| osVersion | String | 否 | - | 系统版本 |
| appVersion | String | 否 | - | app 版本 |
| network | String | 否 | - | 运营商网络 |
| did | String | 否 | - | 设备 ID |
| questionFrom | String | 是 | - | 问题来源 |
| errorScene | String | 否 | - | 报错场景 |
| errorCode | String | 否 | - | 错误码 |
| questionCateOne | int | 是 | 0 | 问题一级分类 |
| questionCateTwo | int | 是 | 0 | 问题二级分类 |
| questionCateThree | int | 否 | 0 | 问题三级分类 |
| questionLabel | String | 否 | - | 问题标签 |
| content | String | 否 | - | 工单备注 |
| callRecord | String | 否 | - | 按键流程类型 |
| callCate | String | 否 | - | 呼入类型 |
| workState | Integer | 是 | - | 工单状态(处理状态,1 创建，2 处理中，3 结案) |
| workDoneJobNo | String | 否 | - | 工单处理工号 |
| workDoneSeats | String | 否 | - | 工单处理坐席工号 |
| workDoneState | int | 否 | 0 | 工单处理类型 |
| workDoneStateStr | String | 否 | - | 工单处理类型文字 |
| linkMode | String | 否 | - | 工单呼入方式 |
| workDoneTime | String | 否 | - | 工单处理时间 yyyy-MM-dd HH:mm:ss |
| workRemark | String | 否 | - | 工单处理备注 |
| callTime | String | 是 | - | 呼入时间 yyyy-MM-dd HH:mm:ss |
| callEndTime | String | 是 | - | 呼入结束时间 yyyy-MM-dd HH:mm:ss |
| callSeatsTime | String | 是 | - | 人工呼入时间 yyyy-MM-dd HH:mm:ss |
| isDraft | int | 是 | 0 | 1 草稿，0 提交 |
| workId | String | 否 | - | 工单 id |
| uploadStr | String | 否 | - | 上传 key，用\|分割 |
| rejectRefundAmount | BigDecimal | 否 | 0 | 婉拒退费金额 |
| searchKeyword | String | 否 | - | 关键词 |
| urgency | Integer | 否 | - | 紧急程度 |
| phoneOrderType | String | 是 | 1 | 1 呼入，2 呼出 |
| disposeDepartId | String | 否 | - | 处理部门 id |
| disposeContent | String | 否 | - | 处理内容 |
| timeoutDays | Integer | 否 | - | 处理时效 |
| calledNumber | String | 否 | - | 被叫号码 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "workOrderId": "WO202401010001", // 工单ID
    "createTime": "2024-01-01 15:30:00", // 创建时间
    "status": "CREATED", // 工单状态
    "priority": "NORMAL", // 优先级：HIGH-高，NORMAL-中，LOW-低
    "estimatedTime": 30, // 预计处理时间(分钟)
    "assignedTo": "agent001", // 分配给谁
    "department": "技术支持部", // 处理部门
    "category": "技术问题", // 问题分类
    "description": "用户反馈的问题描述", // 问题描述
    "contactInfo": {
      // 联系信息
      "name": "张三", // 联系人姓名
      "phone": "13800138000", // 联系电话
      "email": "zhangsan@example.com" // 联系邮箱
    },
    "attachments": [
      // 附件列表
      {
        "id": "att001", // 附件ID
        "name": "screenshot.png", // 附件名称
        "url": "http://example.com/file.png", // 附件URL
        "size": 1024000, // 文件大小(字节)
        "type": "image/png" // 文件类型
      }
    ],
    "history": [
      // 处理历史
      {
        "time": "2024-01-01 15:30:00", // 操作时间
        "operator": "agent001", // 操作人
        "action": "创建工单", // 操作内容
        "comment": "用户提交工单" // 备注
      }
    ]
  }
}
```

### 7. 文件上传签名获取接口

**接口地址**: `GET /api/thirdparty/upload/sign`

**功能描述**: 获取文件上传所需的签名信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| base64 | String | 是 | - | 文件 base64 编码 |
| fileSize | BigInteger | 是 | - | 文件大小(字节) |
| fileMd5 | String | 是 | - | 文件 MD5 值 |
| fileSubfix | String | 是 | - | 文件后缀 |
| platName | String | 是 | - | 平台名称，使用‘mgtv_kf_neibu’ |

**响应示例**:

```json
{
  "code": 200,
  "msg": "成功",
  "data": {
    "bucketInfo": {
      // 存储桶信息
      "bucketName": "mgtv-uploadfile-1258229344", // 存储桶名称
      "hostCdnImage": "https://mgtv-uploadfile-1258229344.cos.ap-beijing.myqcloud.com", // CDN图片域名
      "bucketId": 9, // 存储桶ID
      "cloud": 22, // 云服务商ID
      "bucket": "mgtv-uploadfile-1258229344", // 存储桶
      "imgResizeFormat": "imageMogr2/thumbnail/%dx%d>", // 图片缩放格式
      "endpoint": "cos.ap-beijing.myqcloud.com", // 服务端点
      "hostSource": "https://mgtv-uploadfile-1258229344.cos.ap-beijing.myqcloud.com", // 源站域名
      "cloudName": "COS", // 云服务商名称
      "cloudId": 22, // 云服务商ID
      "hostCdn": "https://mgtv-uploadfile-1258229344.cos.ap-beijing.myqcloud.com", // CDN域名
      "region": "ap-beijing", // 区域
      "hostCdnFile": "https://mgtv-uploadfile-1258229344.cos.ap-beijing.myqcloud.com" // CDN文件域名
    },
    "stsToken": {
      // STS临时凭证
      "accessKeyId": "AKIDd0z5vefdf2wTNvQ15R76y_9hsL1Y3fvAvTRuwRR9u-A4h_Lra1I6XAKf_5-laoh8", // 访问密钥ID
      "securityToken": "rcqPHB16LX5CjB2J01ir20hR2Z7oh0Sa36ffecc92ef08bb0ff5ad2966ba8b2d5pyqWKdtlWm0YjT3h2VJWmDkJ6X7SvNjA0sKuON0i2zGdUX-mOLC08JeVzLapgu0fP5aNcHk7WR7IJXYNcJ7WMS5SXbePdDu-DE5zfepgGapk0A8YWMNKtWIIWijcRzgZZYBrZvO-yHI6oujZbyeysOWBmVORKkvmIeX9y4OfT_9dE7Jp_fSANZ2GUnLHMV58AF7V3OQYooJe88Y0NhxlMgX386r_uPa8TH5zsWpNsGkJaXNi6FDeozk7CEc0_QMv9O_TaAvmwLm5OdN08OfJe0l93ziMEbUl1zWqqFMalsIdU1hB-YrZzSOGwHEtG9cLLv7skHbba9Ow0FZEcUJs8mcTOw0RdZI-FyRXX96UOEEVdijtOyFJ70_B0XnmIVLSt39BnLP-ogRdtc7JCcunfifzaJXdQc-dGStsYhg7gcGdBGrVjWbpW2uxz3mJsAk0QA8MFiEbnUlgriKDa2Be3HIa-yQ9II3F1PlVICCuwsSq-7aby7PEAXeIAnrSeLOgl_xpNXt9kkU820ykBIJjGzzyjtylHSC1TWdTRXHiS7FTjyUfLURpCPCw70nvbN89QkFVuz-PFT3PCHf3Din3PMtK6Zmhc3Ka369_uT5wS2TMGXAebDNCcmQL0svARZH_UQMaHD8GNnzVuGQfQPBOcWVtYrSbEAnE7cx6JrSdAH7W29onFHuFKETmdBxF3YBpv5g7q_qtDBntIRtGG0mabIckk7b82haW8nDPwt8LuBcJQDSYc0EsDYfgieiq94es6FOc-R6rEw8uDLBYnDzLuK8Z_yYYgG_stE_owAnHufNxkGBvOHGZz0p5jJEor3pE24uYsjnEmvkd7LA_6LtOIXiRcyZEPZWp7k5TIxkY62V3rkwjwJ_l7FHuo03WpKOENhzJQ6x2w-27-Des6bfJQFHd_Ae53f0i-KOPKpeGTSU", // 安全令牌
      "accessKeySecret": "XYCVJqpwpu05Ob9KFl65Aw3q/EiYNe4ZrJ/fw64NSJU=", // 访问密钥
      "expiration": "2025-10-15 15:28:18", // 过期时间
      "startTime": 1760511498, // 开始时间戳
      "expiredTime": 1760513298 // 过期时间戳
    },
    "keyInfo": {
      // 文件键信息
      "idstr": "578959489111810048", // 文件ID字符串
      "fileinfo": "{\"env\":\"test\",\"fileid\":\"578959489111810048\"}", // 文件信息JSON
      "subfix": "jpg", // 文件后缀
      "id": 578959489111810048, // 文件ID
      "key": "test/28/2510/1514/5818/AZDQI3ZGSIlgtg6/578959489111810048.jpg" // 文件存储键
    }
  }
}
```

### 8. 文件访问权限设置接口

**接口地址**: `POST /api/thirdparty/upload/setAcl`

**功能描述**: 设置文件的访问权限

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | Long | 是 | - | 文件 ID，对应上一个接口 keyInfo 中的 idstr |
| platName | String | 是 | - | 平台名称，使用‘mgtv_kf_neibu’ |

**响应示例**:

```json
{
  "code": 200,
  "msg": "成功",
  "data": {
    "msg": "success",
    "code": 200,
    "data": null,
    "seqId": "3dd51a35-f9fb-4f13-bce1-a37d125cd71c"
  }
}
```

### 9. 工号和分机号绑定接口

**接口地址**: `POST /api/thirdparty/callcenter/bind`

**功能描述**: 绑定工号和分机号

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| seatsId | String | 是 | - | 工号 |
| extensionNo | String | 是 | - | 分机号 |
| email | String | 否 | - | 坐席邮箱(从请求参数中获取，如果没有则使用默认值) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "bindId": "bind123456", // 绑定ID
    "seatsId": "agent001", // 工号
    "extensionNo": "8001", // 分机号
    "bindTime": "2024-01-01 15:30:00", // 绑定时间
    "status": "ACTIVE", // 绑定状态：ACTIVE-激活，INACTIVE-未激活
    "agentInfo": {
      // 坐席信息
      "name": "张三", // 坐席姓名
      "email": "zhangsan@mgtv.com", // 坐席邮箱
      "department": "客服部", // 所属部门
      "level": "SENIOR", // 坐席等级：JUNIOR-初级，SENIOR-高级，EXPERT-专家
      "skills": ["技术支持", "产品咨询"], // 技能标签
      "status": "ONLINE" // 在线状态：ONLINE-在线，OFFLINE-离线，BUSY-忙碌
    }
  }
}
```

### 10. 软电话登出接口

**接口地址**: `POST /api/thirdparty/callcenter/unBind`

**功能描述**: 解绑工号和分机号

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| seatsId | String | 是 | - | 工号 |
| extensionNo | String | 是 | - | 分机号 |
| email | String | 否 | - | 坐席邮箱(从请求参数中获取，如果没有则使用默认值) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "unbindTime": "2024-01-01 16:30:00", // 解绑时间
    "seatsId": "agent001", // 工号
    "extensionNo": "8001", // 分机号
    "reason": "用户主动登出", // 解绑原因
    "sessionDuration": 3600, // 会话时长(秒)
    "callCount": 5, // 通话次数
    "totalCallTime": 1800 // 总通话时长(秒)
  }
}
```

### 11. 按键随路数据信息接口

**接口地址**: `POST /api/thirdparty/callcenter/recordInfo`

**功能描述**: 查询按键随路数据信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| recordIds | String | 是 | - | 随路按键信息 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "recordId": "12345", // 记录ID
    "callId": "call123456", // 通话ID
    "seatsId": "agent001", // 坐席工号
    "extensionNo": "8001", // 分机号
    "callerNumber": "13800138000", // 主叫号码
    "calledNumber": "4001234567", // 被叫号码
    "startTime": "2024-01-01 15:30:00", // 开始时间
    "endTime": "2024-01-01 15:35:00", // 结束时间
    "duration": 300, // 通话时长(秒)
    "keySequence": "12345", // 按键序列
    "keyDetails": [
      // 按键详情
      {
        "key": "1", // 按键值
        "pressTime": "2024-01-01 15:31:00", // 按键时间
        "duration": 1, // 按键时长(秒)
        "description": "选择技术支持" // 按键描述
      }
    ],
    "callType": "INBOUND", // 通话类型：INBOUND-呼入，OUTBOUND-呼出
    "callResult": "ANSWERED", // 通话结果：ANSWERED-已接听，NO_ANSWER-未接听，BUSY-忙线
    "queueTime": 10, // 排队时长(秒)
    "transferCount": 0, // 转接次数
    "recordingUrl": "http://example.com/recording.wav", // 录音文件URL
    "qualityScore": 85, // 质量评分
    "notes": "用户咨询技术问题" // 备注
  }
}
```

### 12. 小休信息上报接口

**接口地址**: `POST /api/thirdparty/callcenter/seatsAux`

**功能描述**: 上报小休信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| seatsId | String | 是 | - | 坐席编号 |
| extensionNo | String | 否 | - | 分机号 |
| startTime | String | 是 | - | 开始时间 |
| endTime | String | 是 | - | 结束时间 |
| longTime | int | 是 | 0 | 小休时长 秒 |
| auxCate | int | 否 | 1 | 小休类型，1 小休，2 就餐 |
| email | String | 否 | - | 坐席邮箱(从请求参数中获取，如果没有则使用默认值) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "auxId": "aux123456", // 小休记录ID
    "seatsId": "agent001", // 坐席工号
    "extensionNo": "8001", // 分机号
    "startTime": "2024-01-01 12:00:00", // 开始时间
    "endTime": "2024-01-01 13:00:00", // 结束时间
    "duration": 3600, // 小休时长(秒)
    "auxType": "BREAK", // 小休类型：BREAK-小休，MEAL-就餐
    "status": "COMPLETED", // 状态：ACTIVE-进行中，COMPLETED-已完成，CANCELLED-已取消
    "reason": "午餐时间", // 小休原因
    "approver": "supervisor001", // 审批人
    "approvalTime": "2024-01-01 11:55:00", // 审批时间
    "notes": "正常午餐休息" // 备注
  }
}
```

### 13. 通话 ucid 信息上报接口

**接口地址**: `POST /api/thirdparty/callcenter/soundUcid`

**功能描述**: 上报 ucid 信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| ucid | String | 是 | - | 初始唯一编号 |
| changeUcid | String | 是 | - | 改变的 ucid 信息 |
| ucidLable | String | 是 | - | ucid 类型信息 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "soundId": "sound123456", // 语音记录ID
    "originalUcid": "ucid001", // 原始UCID
    "newUcid": "ucid002", // 新UCID
    "changeTime": "2024-01-01 15:30:00", // 变更时间
    "changeType": "TRANSFER", // 变更类型：TRANSFER-转接，MERGE-合并，SPLIT-拆分
    "changeReason": "坐席转接", // 变更原因
    "seatsId": "agent001", // 处理坐席
    "callId": "call123456", // 关联通话ID
    "duration": 180, // 通话时长(秒)
    "quality": "GOOD", // 通话质量：EXCELLENT-优秀，GOOD-良好，POOR-差
    "notes": "用户要求转接至技术支持" // 备注
  }
}
```

### 14. 工单信息列表查询接口

**接口地址**: `GET /api/thirdparty/callcenter/workorder/list`

**功能描述**: 根据访客电话号码查询工单列表

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| visitorPhone | String | 是 | - | 访客电话 |
| merchantid | String | 否 | - | 商户 ID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 25, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 3, // 总页数
    "list": [
      {
        "id": 1001, // 工单 ID
        "visitorName": "张三", // 访客姓名
        "visitorPhone": "13800138000", // 访客电话
        "visitorId": "visitor123", // 访客 ID
        "state": 2, // 工单状态：1-创建，2-处理中，3-结案
        "urgency": 1, // 紧急程度：1-普通，2-紧急，3-非常紧急
        "timeout": "2024-01-02 15:30:00", // 超时时间
        "categoryId": 5, // 分类 ID
        "content": "用户反馈系统登录问题", // 工单内容
        "attachment": "attachments/workorder_1001.zip", // 附件路径
        "attachmentName": "截图.zip", // 附件名称
        "orderType": 1, // 工单类型：1-咨询，2-投诉，3-建议
        "chatId": "chat123456", // 会话 ID
        "disposeUserId": "agent001", // 处理人 ID
        "disposeUserName": "李四", // 处理人姓名
        "disposeContent": "已联系用户解决问题", // 处理内容
        "disposeTime": "2024-01-01 16:00:00", // 处理时间
        "disposeContentCount": 1, // 处理内容数量
        "disposePeroid": 30, // 处理时长(分钟)
        "isTimeout": 0, // 是否超时：0-否，1-是
        "isRetreat": 0, // 是否退回：0-否，1-是
        "editUserId": "agent002", // 编辑人 ID
        "editUserName": "王五", // 编辑人姓名
        "backContent": "", // 退回原因
        "vsid": "vs123", // 访客会话 ID
        "orderSequence": "WO202401010001", // 工单序列号
        "reminder": 0, // 提醒次数
        "isCirculate": 0, // 是否流转：0-否，1-是
        "memberCode": "VIP001", // 会员编码
        "visitorAvatar": "http://example.com/avatar.jpg", // 访客头像
        "callerNumber": "13800138000", // 主叫号码
        "memberType": "VIP", // 会员类型
        "qq": "123456789", // QQ 号码
        "visitorIp": "192.168.1.100", // 访客 IP
        "visitorLocation": "北京市朝阳区", // 访客位置
        "clientType": "web", // 客户端类型
        "deviceBrand": "Apple", // 设备品牌
        "deviceModel": "iPhone 12", // 设备型号
        "systemVersion": "iOS 15.0", // 系统版本
        "mangoVersion": "v2.1.0", // 芒果版本
        "deviceId": "device123", // 设备 ID
        "networkOperator": "中国移动", // 网络运营商
        "problemSource": "用户反馈", // 问题来源
        "errorScene": "登录页面", // 错误场景
        "errorCode": "E001", // 错误码
        "problemType": "会员问题_取消续订1_话费退订1", // 问题类型
        "incomingCallProblemType": "咨询", // 呼入问题类型
        "incomingCallPath": "按键1", // 呼入路径
        "keyRecordInfo": "按键1-技术支持", // 按键记录信息
        "requestTime": "2024-01-01 15:30:00", // 请求时间
        "startTime": "2024-01-01 15:31:00", // 开始时间
        "endTime": "2024-01-01 15:35:00", // 结束时间
        "answerExtension": "8001", // 应答分机
        "thirdPartyAccount": "agent001", // 第三方账号
        "calledNumber": "4001234567", // 被叫号码
        "phoneOrderType": "1", // 电话工单类型：1-呼入，2-呼出
        "state2": 1, // 工单状态2：0-未处理，1-已处理，2-转交处理
        "connectionMethod": "电话", // 连接方式
        "questionLabel": "技术问题", // 问题标签
        "createTime": "2024-01-01 15:30:00", // 创建时间
        "updateTime": "2024-01-01 16:00:00", // 更新时间
        "merchantid": "merchant001", // 商户 ID
        "createId": "system", // 创建人 ID
        "createName": "系统", // 创建人姓名
        "createDepartId": "1", // 创建部门 ID
        "disposeDepartId": "2", // 处理部门 ID
        "disposeDepartName": "客服管理员", // 处理部门名称
        "rejectRefundAmount": 0.0, // 婉拒退费金额
        "searchKeyword": "登录问题", // 关键词
        "firstProlemLabel": "系统问题", // 一级分类标签
        "secondProlemLabel": "登录异常", // 二级分类标签
        "thirdProlemLabel": "账号锁定", // 三级分类标签
        "timeoutDays": 1, // 处理时限(天)
        "province": 110000, // 省ID
        "city": 110100, // 市ID
        "district": 110101, // 区ID
        "questionCategoryIds": "1,2,3", // 问题分类ID，多个逗号拼接
        "questionCateOne": 1, // 问题一级分类ID
        "questionCateTwo": 2, // 问题二级分类ID
        "questionCateThree": 3 // 问题三级分类ID
      }
    ]
  }
}
```

### 15. 工单信息详情查询接口

**接口地址**: `GET /api/thirdparty/callcenter/workorder/get`

**功能描述**: 根据工单 ID 查询工单详细信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| id | Integer | 是 | - | 工单 ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "id": 1001, // 工单 ID
    "merchantid": "merchant001", // 商户 ID
    "createTime": "2024-01-01 15:30:00", // 创建时间
    "createId": "system", // 创建人 ID
    "createName": "系统", // 创建人姓名
    "createDepartId": "1", // 创建部门 ID
    "disposeDepartId": "2", // 处理部门 ID
    "disposeDepartName": "客服管理员", // 处理部门名称
    "visitorName": "张三", // 访客姓名
    "visitorPhone": "13800138000", // 访客电话
    "visitorId": "visitor123", // 访客 ID
    "state": 2, // 工单状态：1-创建，2-处理中，3-结案
    "urgency": 1, // 紧急程度：1-普通，2-紧急，3-非常紧急
    "timeout": "2024-01-02 15:30:00", // 超时时间
    "categoryId": 5, // 分类 ID
    "content": "用户反馈系统登录问题，无法正常登录账号", // 工单内容
    "attachment": "attachments/workorder_1001.zip", // 附件路径
    "attachmentName": "截图.zip", // 附件名称
    "orderType": 1, // 工单类型：1-咨询，2-投诉，3-建议
    "chatId": "chat123456", // 会话 ID
    "disposeUserId": "agent001", // 处理人 ID
    "disposeUserName": "李四", // 处理人姓名
    "disposeContent": "已联系用户解决问题，用户表示问题已解决", // 处理内容
    "disposeTime": "2024-01-01 16:00:00", // 处理时间
    "disposeContentCount": 1, // 处理内容数量
    "disposePeroid": 30, // 处理时长(分钟)
    "isTimeout": 0, // 是否超时：0-否，1-是
    "isRetreat": 0, // 是否退回：0-否，1-是
    "editUserId": "agent002", // 编辑人 ID
    "editUserName": "王五", // 编辑人姓名
    "backContent": "", // 退回原因
    "vsid": "vs123", // 访客会话 ID
    "orderSequence": "WO202401010001", // 工单序列号
    "reminder": 0, // 提醒次数
    "isCirculate": 0, // 是否流转：0-否，1-是
    "memberCode": "VIP001", // 会员编码
    "visitorAvatar": "http://example.com/avatar.jpg", // 访客头像
    "callerNumber": "13800138000", // 主叫号码
    "memberType": "VIP", // 会员类型
    "qq": "123456789", // QQ 号码
    "visitorIp": "192.168.1.100", // 访客 IP
    "visitorLocation": "北京市朝阳区", // 访客位置
    "clientType": "web", // 客户端类型
    "deviceBrand": "Apple", // 设备品牌
    "deviceModel": "iPhone 12", // 设备型号
    "systemVersion": "iOS 15.0", // 系统版本
    "mangoVersion": "v2.1.0", // 芒果版本
    "deviceId": "device123", // 设备 ID
    "networkOperator": "中国移动", // 网络运营商
    "problemSource": "用户反馈", // 问题来源
    "errorScene": "登录页面", // 错误场景
    "errorCode": "E001", // 错误码
    "problemType": "系统故障", // 问题类型
    "incomingCallProblemType": "咨询", // 呼入问题类型
    "incomingCallPath": "按键1", // 呼入路径
    "keyRecordInfo": "按键1-技术支持", // 按键记录信息
    "requestTime": "2024-01-01 15:30:00", // 请求时间
    "startTime": "2024-01-01 15:31:00", // 开始时间
    "endTime": "2024-01-01 15:35:00", // 结束时间
    "answerExtension": "8001", // 应答分机
    "thirdPartyAccount": "agent001", // 第三方账号
    "calledNumber": "4001234567", // 被叫号码
    "phoneOrderType": "1", // 电话工单类型：1-呼入，2-呼出
    "state2": 1, // 工单状态2：0-未处理，1-已处理，2-转交处理
    "connectionMethod": "电话", // 连接方式
    "questionLabel": "技术问题", // 问题标签
    "rejectRefundAmount": 0.0, // 婉拒退费金额
    "searchKeyword": "登录问题", // 关键词
    "firstProlemLabel": "系统问题", // 一级分类标签
    "secondProlemLabel": "登录异常", // 二级分类标签
    "thirdProlemLabel": "账号锁定", // 三级分类标签
    "timeoutDays": 1, // 处理时限(天)
    "province": 110000, // 省ID
    "city": 110100, // 市ID
    "district": 110101, // 区ID
    "questionCategoryIds": "1,2,3", // 问题分类ID，多个逗号拼接
    "questionCateOne": 1, // 问题一级分类ID
    "questionCateTwo": 2, // 问题二级分类ID
    "questionCateThree": 3, // 问题三级分类ID
    "remarkList": [
      // 工单备注列表
      {
        "id": 1, // 备注ID
        "orderId": 1001, // 工单ID
        "content": "用户反馈登录问题已解决", // 备注内容
        "createId": "agent001", // 创建人ID
        "createName": "李四", // 创建人姓名
        "createTime": "2024-01-01 16:00:00", // 创建时间
        "type": 5, // 备注类型：0-新建，3-再次流转，4-已解决，5-备注，7-修改，8-重新启用
        "status2": 2, // 备注状态：0-草稿，1-用户不配合关闭，2-已解决，3-待回访，4-待二线解决，5-待处理，6-待业务支撑解决，7-业务支撑解决，8-二线解决，9-回访解决
        "formatContent": "备注 已解决 李四 2024-01-01 16:00:00" // 格式化的内容
      },
      {
        "id": 2, // 备注ID
        "orderId": 1001, // 工单ID
        "content": "已联系用户确认问题解决", // 备注内容
        "createId": "agent001", // 创建人ID
        "createName": "李四", // 创建人姓名
        "createTime": "2024-01-01 16:30:00", // 创建时间
        "type": 4, // 备注类型：0-新建，3-再次流转，4-已解决，5-备注，7-修改，8-重新启用
        "status2": 2, // 备注状态：0-草稿，1-用户不配合关闭，2-已解决，3-待回访，4-待二线解决，5-待处理，6-待业务支撑解决，7-业务支撑解决，8-二线解决，9-回访解决
        "formatContent": "已解决 已解决 李四 2024-01-01 16:30:00" // 格式化的内容
      }
    ]
  }
}
```

### 16. 会员多重身份查询接口

**接口地址**: `GET /api/thirdparty/member/info`

**功能描述**: 获取第三方会员多重身份信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |
| did | String | 否 | default | 设备 ID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "uuid": "user123456", // 用户唯一标识
    "username": "张三", // 用户名
    "nickname": "小张", // 昵称
    "mobile": "13800138000", // 手机号码
    "email": "zhangsan@example.com", // 邮箱地址
    "avatar": "http://example.com/avatar.jpg", // 头像 URL
    "gender": 1, // 性别：1-男，2-女，0-未知
    "birthday": "1990-01-01", // 生日
    "registerTime": "2020-01-01 10:00:00", // 注册时间
    "lastLoginTime": "2024-01-01 15:30:00", // 最后登录时间
    "status": 1, // 用户状态：1-正常，0-禁用
    "vipLevel": 2, // VIP 等级
    "points": 1000, // 积分
    "realName": "张三", // 真实姓名
    "idCard": "110101199001011234", // 身份证号
    "address": "北京市朝阳区", // 地址
    "qq": "123456789", // QQ 号
    "wechat": "zhangsan123", // 微信号
    "memberInfo": {
      // 会员信息
      "memberId": "VIP001", // 会员 ID
      "memberType": "VIP", // 会员类型：VIP-会员，SVIP-超级会员，NORMAL-普通用户
      "memberLevel": 2, // 会员等级
      "memberStatus": 1, // 会员状态：1-正常，0-冻结
      "expireTime": "2024-12-31 23:59:59", // 会员到期时间
      "totalAmount": 999.0, // 累计消费金额
      "balance": 100.0, // 账户余额
      "points": 5000, // 会员积分
      "growthValue": 2000, // 成长值
      "joinTime": "2020-01-01 10:00:00" // 加入会员时间
    },
    "deviceInfo": {
      // 设备信息
      "deviceId": "device123", // 设备 ID
      "deviceType": "mobile", // 设备类型：mobile-手机，pc-电脑，tv-电视
      "deviceBrand": "Apple", // 设备品牌
      "deviceModel": "iPhone 12", // 设备型号
      "osVersion": "iOS 15.0", // 系统版本
      "appVersion": "v2.1.0", // 应用版本
      "lastLoginDevice": "iPhone 12", // 最后登录设备
      "lastLoginTime": "2024-01-01 15:30:00" // 最后登录时间
    }
  }
}
```

### 17. 会员订单信息查询接口

**接口地址**: `GET /api/thirdparty/member/orderList`

**功能描述**: 第三方会员订单信息查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 50, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 5, // 总页数
    "list": [
      {
        "orderId": "ORD202401010001", // 订单 ID
        "orderNo": "202401010001", // 订单编号
        "orderType": 1, // 订单类型：1-会员购买，2-观影券购买，3-代金券购买，4-其他
        "orderTypeName": "会员购买", // 订单类型名称
        "orderStatus": 2, // 订单状态：1-待支付，2-已支付，3-已取消，4-已退款
        "orderStatusName": "已支付", // 订单状态名称
        "productId": "VIP001", // 产品 ID
        "productName": "芒果 TV 会员年卡", // 产品名称
        "productType": "VIP", // 产品类型
        "amount": 199.0, // 订单金额
        "payAmount": 199.0, // 实付金额
        "discountAmount": 0.0, // 优惠金额
        "payMethod": "微信支付", // 支付方式
        "payTime": "2024-01-01 15:30:00", // 支付时间
        "createTime": "2024-01-01 15:25:00", // 创建时间
        "updateTime": "2024-01-01 15:30:00", // 更新时间
        "expireTime": "2025-01-01 23:59:59", // 到期时间
        "description": "芒果 TV 会员年卡，享受会员特权", // 订单描述
        "remark": "用户主动购买", // 订单备注
        "channel": "APP", // 购买渠道：APP-应用，WEB-网页，TV-电视
        "operator": "system", // 操作人
        "refundStatus": 0, // 退款状态：0-未退款，1-退款中，2-已退款
        "refundAmount": 0.0, // 退款金额
        "refundTime": null, // 退款时间
        "refundReason": "", // 退款原因
        "couponUsed": "COUPON001", // 使用的优惠券
        "couponAmount": 20.0, // 优惠券金额
        "memberLevel": 2, // 会员等级
        "memberType": "VIP" // 会员类型
      }
    ]
  }
}
```

### 18. 会员服务信息查询接口

**接口地址**: `GET /api/thirdparty/member/serverList`

**功能描述**: 第三方会员服务信息查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 20, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 2, // 总页数
    "list": [
      {
        "serviceId": "SER001", // 服务 ID
        "serviceName": "芒果 TV 会员服务", // 服务名称
        "serviceType": "VIP", // 服务类型：VIP-会员服务，COUPON-观影券服务，VOUCHER-代金券服务
        "serviceStatus": 1, // 服务状态：1-正常，2-暂停，3-已过期
        "serviceStatusName": "正常", // 服务状态名称
        "startTime": "2024-01-01 00:00:00", // 服务开始时间
        "endTime": "2024-12-31 23:59:59", // 服务结束时间
        "duration": 365, // 服务时长(天)
        "remainingDays": 300, // 剩余天数
        "serviceLevel": 2, // 服务等级
        "serviceDescription": "享受会员专属内容，无广告观看", // 服务描述
        "benefits": [
          // 服务权益
          "无广告观看",
          "会员专属内容",
          "1080P 高清画质",
          "多设备登录"
        ],
        "autoRenew": 1, // 自动续费：0-关闭，1-开启
        "renewPrice": 199.0, // 续费价格
        "renewCycle": "year", // 续费周期：month-月，year-年
        "createTime": "2024-01-01 10:00:00", // 创建时间
        "updateTime": "2024-01-01 15:30:00", // 更新时间
        "operator": "system", // 操作人
        "remark": "用户主动购买" // 备注
      }
    ]
  }
}
```

### 19. 会员观影券查询接口

**接口地址**: `GET /api/thirdparty/member/couponList`

**功能描述**: 第三方会员观影券查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |
| couponLevel | Integer | 否 | 2 | 观影券等级：1-普通券，2-高级券 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 15, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 2, // 总页数
    "list": [
      {
        "couponId": "COUPON001", // 观影券 ID
        "couponCode": "MGTV20240101001", // 观影券码
        "couponName": "芒果 TV 观影券", // 观影券名称
        "couponType": 1, // 观影券类型：1-单次券，2-多次券，3-无限券
        "couponTypeName": "单次券", // 观影券类型名称
        "couponLevel": 2, // 观影券等级：1-普通券，2-高级券
        "couponLevelName": "高级券", // 观影券等级名称
        "couponValue": 1, // 观影券面值(次数)
        "usedValue": 0, // 已使用次数
        "remainingValue": 1, // 剩余次数
        "validStartTime": "2024-01-01 00:00:00", // 有效期开始时间
        "validEndTime": "2024-12-31 23:59:59", // 有效期结束时间
        "status": 1, // 状态：1-未使用，2-已使用，3-已过期，4-已作废
        "statusName": "未使用", // 状态名称
        "useTime": null, // 使用时间
        "useContent": "", // 使用内容
        "createTime": "2024-01-01 10:00:00", // 创建时间
        "updateTime": "2024-01-01 10:00:00", // 更新时间
        "source": "购买", // 来源：购买-用户购买，赠送-系统赠送，活动-活动获得
        "sourceOrderId": "ORD202401010001", // 来源订单 ID
        "description": "可观看任意一部电影", // 观影券描述
        "restrictions": "仅限芒果 TV 平台使用", // 使用限制
        "operator": "system" // 操作人
      }
    ]
  }
}
```

### 20. 会员代金券查询接口

**接口地址**: `GET /api/thirdparty/member/voucherList`

**功能描述**: 第三方会员代金券查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |
| voucherType | Integer | 否 | 0 | 代金券类型：0-全部，1-通用券，2-会员券，3-观影券 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 25, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 3, // 总页数
    "list": [
      {
        "voucherId": "VOUCHER001", // 代金券 ID
        "voucherCode": "MGTV20240101001", // 代金券码
        "voucherName": "芒果 TV 代金券", // 代金券名称
        "voucherType": 1, // 代金券类型：1-通用券，2-会员券，3-观影券
        "voucherTypeName": "通用券", // 代金券类型名称
        "voucherAmount": 50.0, // 代金券面额
        "minAmount": 100.0, // 最低使用金额
        "discountAmount": 50.0, // 优惠金额
        "validStartTime": "2024-01-01 00:00:00", // 有效期开始时间
        "validEndTime": "2024-12-31 23:59:59", // 有效期结束时间
        "status": 1, // 状态：1-未使用，2-已使用，3-已过期，4-已作废
        "statusName": "未使用", // 状态名称
        "useTime": null, // 使用时间
        "useOrderId": "", // 使用订单 ID
        "createTime": "2024-01-01 10:00:00", // 创建时间
        "updateTime": "2024-01-01 10:00:00", // 更新时间
        "source": "活动", // 来源：购买-用户购买，赠送-系统赠送，活动-活动获得
        "sourceOrderId": "", // 来源订单 ID
        "description": "满 100 元可用，立减 50 元", // 代金券描述
        "restrictions": "仅限芒果 TV 平台使用，不可与其他优惠同时使用", // 使用限制
        "applicableProducts": [
          // 适用产品
          "会员服务",
          "观影券",
          "其他服务"
        ],
        "operator": "system" // 操作人
      }
    ]
  }
}
```

### 21. 会员退费订单查询接口

**接口地址**: `GET /api/thirdparty/member/refundList`

**功能描述**: 第三方会员退费订单查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 8, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 1, // 总页数
    "list": [
      {
        "refundId": "REFUND001", // 退款 ID
        "refundNo": "RF202401010001", // 退款编号
        "orderId": "ORD202401010001", // 原订单 ID
        "orderNo": "202401010001", // 原订单编号
        "refundType": 1, // 退款类型：1-全额退款，2-部分退款，3-换货
        "refundTypeName": "全额退款", // 退款类型名称
        "refundStatus": 2, // 退款状态：1-申请中，2-已退款，3-已拒绝，4-已取消
        "refundStatusName": "已退款", // 退款状态名称
        "refundAmount": 199.0, // 退款金额
        "originalAmount": 199.0, // 原订单金额
        "refundMethod": "原路退回", // 退款方式
        "refundAccount": "微信支付", // 退款账户
        "refundReason": "用户主动申请退款", // 退款原因
        "refundDescription": "用户对服务不满意，申请退款", // 退款说明
        "applyTime": "2024-01-01 16:00:00", // 申请时间
        "processTime": "2024-01-01 16:30:00", // 处理时间
        "refundTime": "2024-01-01 17:00:00", // 退款时间
        "createTime": "2024-01-01 16:00:00", // 创建时间
        "updateTime": "2024-01-01 17:00:00", // 更新时间
        "operator": "客服001", // 操作人
        "operatorName": "李四", // 操作人姓名
        "remark": "用户对服务不满意", // 备注
        "attachments": [
          // 附件
          {
            "id": "att001", // 附件 ID
            "name": "退款申请.jpg", // 附件名称
            "url": "http://example.com/attachments/refund.jpg", // 附件 URL
            "size": 1024000, // 文件大小(字节)
            "type": "image/jpeg" // 文件类型
          }
        ],
        "productInfo": {
          // 产品信息
          "productId": "VIP001", // 产品 ID
          "productName": "芒果 TV 会员年卡", // 产品名称
          "productType": "VIP", // 产品类型
          "serviceStartTime": "2024-01-01 00:00:00", // 服务开始时间
          "serviceEndTime": "2024-12-31 23:59:59" // 服务结束时间
        }
      }
    ]
  }
}
```

### 22. 用户行为记录信息查询接口

**接口地址**: `GET /api/thirdparty/member/userBehavior`

**功能描述**: 第三方用户行为记录信息查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 150, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 15, // 总页数
    "list": [
      {
        "id": 1001, // 记录 ID
        "uuid": "user123456", // 用户 UUID
        "action": "login", // 行为类型：login-登录，logout-登出，watch-观看，search-搜索，download-下载
        "invoker": "web", // 终端名：web-网页，mobile-手机，tv-电视，pc-电脑
        "ipcode": "110000", // 城市代码
        "ip": "192.168.1.100", // IP 地址
        "btime": 1640995200, // 行为时间戳
        "deviceid": "device123", // 设备 ID
        "os": "iOS 15.0", // 操作系统
        "imei": "123456789012345", // 设备 IMEI
        "mac": "AA:BB:CC:DD:EE:FF", // MAC 地址
        "appversion": "v2.1.0", // 应用版本
        "phonetype": "iPhone 12", // 手机型号
        "status": 1, // 状态：1-正常，0-异常
        "dname": "iPhone 12", // 设备名称
        "ticket": "ticket123456", // 登录票据
        "isvalid": 1, // 是否有效：1-有效，0-无效
        "ttime": 1640995200, // 时间戳
        "kickreason": 0, // 踢出原因：0-无，1-异地登录，2-账号异常，3-系统维护
        "loginreason": 0, // 登录原因：0-正常登录，1-自动登录，2-第三方登录
        "createTime": "2024-01-01 15:30:00", // 创建时间
        "updateTime": "2024-01-01 15:30:00" // 更新时间
      }
    ]
  }
}
```

### 23. 用户播放记录信息查询接口

**接口地址**: `GET /api/thirdparty/member/playHistory`

**功能描述**: 第三方用户播放记录信息查询

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |
| date | String | 否 | 当天 | 查询日期(格式：yyyy.MM.dd) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 80, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 8, // 总页数
    "list": [
      {
        "id": 1001, // 记录 ID
        "datetime": "2024-01-01 15:30:00", // 播放时间
        "invoker": "web", // 终端类型：web-网页，mobile-手机，tv-电视，pc-电脑
        "videoName": "芒果 TV 热门剧集", // 视频名称
        "assetName": "芒果 TV 热门剧集合集", // 合集名称
        "partId": "part123", // 视频 ID
        "assetId": "asset456", // 合集 ID
        "chargeType": "1", // 是否收费：0-免费，1-收费
        "authResult": "pass", // 观看结果：pass-通过，fail-失败
        "uip": "192.168.1.100", // 用户 IP
        "action": "authPlay", // 操作类型：authPlay-点播播放，authLive-直播播放，authPage-页面访问
        "createTime": "2024-01-01 15:30:00", // 创建时间
        "updateTime": "2024-01-01 15:30:00" // 更新时间
      }
    ]
  }
}
```

### 24. 录音播放地址接口

**接口地址**: `GET /api/thirdparty/callcenter/sound/url`

**功能描述**: 获取录音播放重定向地址

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| ucid | String | 是 | - | 通话唯一编号 |
| callTime | String | 是 | - | 呼入时间(yyyy-MM-dd HH:mm:ss) |
| callEndTime | String | 是 | - | 呼入结束时间(yyyy-MM-dd HH:mm:ss) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": "https://example.com/redirect?token=xxx"
}
```

### 25. 电话号码判断 VIP 接口

**接口地址**: `POST /api/thirdparty/callcenter/checkVip`

**功能描述**: 根据电话号码判断是否 VIP

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| ani | String | 是 | - | 呼叫号码 |
| ucid | String | 是 | - | 通话唯一编号 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": { "isVip": 1 }
}
```

### 26. 短信按 code 发送接口

**接口地址**: `POST /api/thirdparty/callcenter/sendMsgByCode`

**功能描述**: 通过短信映射码发送短信

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| ani | String | 是 | - | 呼叫号码(手机号) |
| ucid | String | 是 | - | 通话唯一编号 |
| msgCode | String | 是 | - | 短信码映射 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {}
}
```

### 27. 满意度调查上报接口

**接口地址**: `POST /api/thirdparty/callcenter/satisfaction`

**功能描述**: 上报满意度信息

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| dnis | String | 是 | - | 被呼叫号码 |
| ani | String | 是 | - | 呼叫号码 |
| ucid | String | 是 | - | 通话唯一编号 |
| seatsId | String | 是 | - | 坐席编号 |
| satisfaction | String | 是 | - | 满意度 |
| callTime | String | 是 | - | 呼入时间(yyyy-MM-dd HH:mm:ss) |
| callEndTime | String | 是 | - | 结束时间(yyyy-MM-dd HH:mm:ss) |
| extensionNo | String | 是 | - | 分机号 |
| source | String | 是 | - | 来源 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {}
}
```

### 28. 按键随录数据上报接口

**接口地址**: `POST /api/thirdparty/callcenter/record`

**功能描述**: 上报随录按键数据

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| dnis | String | 是 | - | 被呼叫号码 |
| ani | String | 否 | - | 呼叫号码 |
| ucid | String | 是 | - | 通话唯一编号 |
| record | String | 是 | - | 随录信息(如 123) |
| recordId | String | 是 | - | 随录流水 ID |
| callTime | String | 是 | - | 呼入时间(yyyy-MM-dd HH:mm:ss) |
| callEndTime | String | 是 | - | 结束时间(yyyy-MM-dd HH:mm:ss) |
| source | String | 是 | - | 来源 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {}
}
```

### 29. 字典列表查询接口

**接口地址**: `GET /api/thirdparty/callcenter/dictList`

**功能描述**: 查询字典列表

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| groupCode | String | 是 | - | 字典类型 |
| name | String | 否 | - | 字典名称(模糊查询) |
| isShowDisabled | Boolean | 否 | false | 是否显示禁用项 |
![img.png](img.png)
**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": [
    {
      "id": 1, // 字典ID
      "groupCode": "PROBLEM_TYPE", // 字典类型
      "name": "技术问题", // 字典名称
      "value": "TECH", // 字典值
      "sort": 1, // 排序
      "status": 1, // 状态：1-启用，0-禁用
      "description": "技术相关问题", // 描述
      "createTime": "2024-01-01 10:00:00", // 创建时间
      "updateTime": "2024-01-01 15:30:00" // 更新时间
    }
  ]
}
```

### 30. 获取组织列表接口

**接口地址**: `GET /api/thirdparty/callcenter/organ/list`

**功能描述**: 获取组织树形结构列表

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| appid | String | 是 | - | 第三方应用 ID |
| sign | String | 是 | - | 签名值 |
| timestamp | Long | 是 | - | 时间戳(毫秒) |
| uuid | String | 是 | - | 用户 UUID |

**响应示例**:

```json
{
  "code": 200,
  "data": [
    {
      "id": "8aaba99e8146786a0181467ae3b90000",
      "createTime": "2022-06-09T03:21:08.000+00:00",
      "updateTime": "2022-09-19T09:56:01.000+00:00",
      "createrId": "0",
      "updaterId": "0",
      "bizId": "2",
      "resCode": "01",
      "resName": "芒果TV", // 处理部门名称
      "pid": "0",
      "longCode": "0!01",
      "longName": "芒果TV",
      "hasChildren": 1,
      "sort": 1,
      "merchantid": "1",
      "children": [
        {
          "id": "8aaba99e8165fd3c0181664edcd90003",
          "createTime": "2022-06-15T07:40:53.000+00:00",
          "updateTime": "2022-09-19T09:56:01.000+00:00",
          "createrId": "0",
          "updaterId": "0",
          "bizId": "2",
          "resCode": "123",
          "resName": "客服管理员",
          "pid": "8aaba99e8146786a0181467ae3b90000",
          "longCode": "0!01!123",
          "longName": "芒果TV/客服管理员",
          "hasChildren": 0,
          "sort": 1,
          "merchantid": "1",
          "children": null
        },
        {
          "id": "8aaba99e8166cf57018167595f470005",
          "createTime": "2022-06-15T12:31:59.000+00:00",
          "updateTime": "2022-09-19T09:56:01.000+00:00",
          "createrId": "0",
          "updaterId": "0",
          "bizId": "2",
          "resCode": "4",
          "resName": "客服人员",
          "pid": "8aaba99e8146786a0181467ae3b90000",
          "longCode": "0!01!4",
          "longName": "芒果TV/客服人员",
          "hasChildren": 0,
          "sort": 2,
          "merchantid": "1",
          "children": null
        }
      ]
    }
  ],
  "msg": ""
}
```

### 31. 第三方管理员登录接口

**接口地址**: `POST /api/thirdparty/admin/login`

**功能描述**: 完全信任第三方，使用邮箱来获取对应客服的登录 token

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| appid | String | 是 | - | 第三方应用 ID |
| sign | String | 是 | - | 签名值 |
| timestamp | Long | 是 | - | 时间戳(毫秒) |
| uuid | String | 是 | - | 用户 UUID |
| email | String | 是 | - | 邮箱地址 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...", // 登录token
    "username": "张三" // 用户名
  }
}
```

### 32. 第三方 CRM 查询工具首页接口

**接口地址**: `POST /api/thirdparty/support/index`

**功能描述**: 返回第三方查询工具首页模板或外部 iframe

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| ottExtend | String | 否 | - | 扩展参数 |
| saasType | Integer | 否 | 0 | 小芒 SaaS 开关(1 启用) |
| otherInfo | String | 否 | 0 | 其他透传信息(JSON) |

**响应示例**:

当 `saasType` 为 1 时，返回 iframe HTML：

```html
<iframe
  frameborder="0"
  height="100%"
  width="100%"
  src="https://ecom.mgtv.com/work-sheet.html?uuid=user123&company_id=1&chat_id=chat123&login_name=admin&goodsId=goods001&user_id=user123"
></iframe>
```

当 `saasType` 为 0 时，返回模板页面内容。

### 33. 第三方 CRM 呼叫中心工具首页接口

**接口地址**: `POST /api/thirdparty/support/callIndex`

**功能描述**: 返回第三方呼叫中心查询工具首页模板

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |

**响应示例**:

返回呼叫中心工具首页模板内容。

### 34. 第三方用户行为记录信息查询接口

**接口地址**: `GET /api/thirdparty/support/userBehavior`

**功能描述**: 登录用户的行为记录查询，需要第三方鉴权

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 150, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 15, // 总页数
    "list": [
      {
        "id": 1001, // 记录 ID
        "uuid": "user123456", // 用户 UUID
        "action": "login", // 行为类型：login-登录，logout-登出，watch-观看，search-搜索，download-下载
        "invoker": "web", // 终端名：web-网页，mobile-手机，tv-电视，pc-电脑
        "ipcode": "110000", // 城市代码
        "ip": "192.168.1.100", // IP 地址
        "btime": 1640995200, // 行为时间戳
        "deviceid": "device123", // 设备 ID
        "os": "iOS 15.0", // 操作系统
        "imei": "123456789012345", // 设备 IMEI
        "mac": "AA:BB:CC:DD:EE:FF", // MAC 地址
        "appversion": "v2.1.0", // 应用版本
        "phonetype": "iPhone 12", // 手机型号
        "status": 1, // 状态：1-正常，0-异常
        "dname": "iPhone 12", // 设备名称
        "ticket": "ticket123456", // 登录票据
        "isvalid": 1, // 是否有效：1-有效，0-无效
        "ttime": 1640995200, // 时间戳
        "kickreason": 0, // 踢出原因：0-无，1-异地登录，2-账号异常，3-系统维护
        "loginreason": 0, // 登录原因：0-正常登录，1-自动登录，2-第三方登录
        "createTime": "2024-01-01 15:30:00", // 创建时间
        "updateTime": "2024-01-01 15:30:00" // 更新时间
      }
    ]
  }
}
```

### 35. 第三方用户播放记录信息查询接口

**接口地址**: `GET /api/thirdparty/support/playHistory`

**功能描述**: 登录用户的播放记录查询，需要第三方鉴权

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |
| page | Integer | 否 | 1 | 页码 |
| pageSize | Integer | 否 | 10 | 每页大小 |
| date | String | 否 | 当天 | 查询日期(格式：yyyy.MM.dd) |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "total": 80, // 总记录数
    "page": 1, // 当前页码
    "pageSize": 10, // 每页大小
    "totalPages": 8, // 总页数
    "list": [
      {
        "id": 1001, // 记录 ID
        "datetime": "2024-01-01 15:30:00", // 播放时间
        "invoker": "web", // 终端类型：web-网页，mobile-手机，tv-电视，pc-电脑
        "videoName": "芒果 TV 热门剧集", // 视频名称
        "assetName": "芒果 TV 热门剧集合集", // 合集名称
        "partId": "part123", // 视频 ID
        "assetId": "asset456", // 合集 ID
        "chargeType": "1", // 是否收费：0-免费，1-收费
        "authResult": "pass", // 观看结果：pass-通过，fail-失败
        "uip": "192.168.1.100", // 用户 IP
        "action": "authPlay", // 操作类型：authPlay-点播播放，authLive-直播播放，authPage-页面访问
        "createTime": "2024-01-01 15:30:00", // 创建时间
        "updateTime": "2024-01-01 15:30:00" // 更新时间
      }
    ]
  }
}
```

### 36. 第三方用户信息查询接口

**接口地址**: `GET /api/thirdparty/user/info`

**功能描述**: 根据 UUID 查询用户详情与登录信息，需要第三方鉴权

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "userInfo": {
      // 用户基本信息
      "uuid": "user123456", // 用户唯一标识
      "username": "张三", // 用户名
      "nickname": "小张", // 昵称
      "mobile": "13800138000", // 手机号码
      "email": "zhangsan@example.com", // 邮箱地址
      "avatar": "http://example.com/avatar.jpg", // 头像URL
      "gender": 1, // 性别：1-男，2-女，0-未知
      "birthday": "1990-01-01", // 生日
      "registerTime": "2020-01-01 10:00:00", // 注册时间
      "lastLoginTime": "2024-01-01 15:30:00", // 最后登录时间
      "status": 1, // 用户状态：1-正常，0-禁用
      "vipLevel": 2, // VIP等级
      "points": 1000, // 积分
      "realName": "张三", // 真实姓名
      "idCard": "110101199001011234", // 身份证号
      "address": "北京市朝阳区", // 地址
      "qq": "123456789", // QQ号
      "wechat": "zhangsan123" // 微信号
    },
    "userLogin": [
      // 用户登录记录
      {
        "id": 1, // 登录记录ID
        "uuid": "user123456", // 用户UUID
        "loginTime": "2024-01-01 15:30:00", // 登录时间
        "logoutTime": "2024-01-01 18:30:00", // 登出时间
        "loginIp": "192.168.1.100", // 登录IP
        "deviceType": "web", // 设备类型
        "deviceInfo": "Chrome 120.0", // 设备信息
        "loginStatus": 1, // 登录状态：1-正常，0-异常
        "sessionDuration": 10800, // 会话时长(秒)
        "location": "北京市朝阳区" // 登录地点
      }
    ]
  }
}
```

### 37. 第三方用户重置接口

**接口地址**: `GET /api/thirdparty/user/reset`

**功能描述**: 重置用户信息，需要第三方鉴权

**请求参数**:
| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| uuid | String | 是 | - | 用户 UUID |

**响应示例**:

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "resetTime": "2024-01-01 16:00:00", // 重置时间
    "resetStatus": "SUCCESS", // 重置状态：SUCCESS-成功，FAILED-失败
    "resetType": "USER_INFO", // 重置类型
    "affectedFields": [
      // 受影响的字段
      "password",
      "loginStatus",
      "sessionToken"
    ],
    "message": "用户信息重置成功", // 重置消息
    "nextAction": "用户需要重新登录" // 下一步操作提示
  }
}
```

## 错误码说明

| 错误码 | 说明           |
| ------ | -------------- |
| 200    | 成功           |
| 1000   | 参数错误       |
| 1001   | 必填参数缺失   |
| 1002   | 业务处理失败   |
| 2001   | 用户信息不存在 |
| 5000   | 系统异常       |

## 注意事项

1. 所有时间格式均为 `yyyy-MM-dd HH:mm:ss`
2. 文件上传需要先调用上传授权接口获取临时凭证
3. 工单状态变更需要遵循状态流转规则
4. 坐席绑定/解绑操作会影响通话分配
5. 所有接口都需要进行第三方鉴权验证

## 请求示例

### 用户信息查询请求示例

```bash
curl -X POST "http://api.example.com/api/thirdparty/callcenter/userInfo" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&username=13800138000"
```

### 工单提交请求示例

```bash
curl -X POST "http://api.example.com/api/thirdparty/callcenter/workOrder/create" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&mobile=13800138000&ucid=call123456&clientType=web&questionFrom=用户反馈&questionCateOne=1&questionCateTwo=2&workState=1&callTime=2024-01-01 15:30:00&callEndTime=2024-01-01 15:35:00&callSeatsTime=2024-01-01 15:31:00&isDraft=0&phoneOrderType=1"
```

### 工单列表查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/callcenter/workorder/list?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&visitorPhone=13800138000&page=1&pageSize=10"
```

### 工单详情查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/callcenter/workorder/get?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&id=1001"
```

### 会员多重身份查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/info?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10&did=device123"
```

### 会员订单信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/orderList?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10"
```

### 会员服务信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/serverList?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10"
```

### 会员观影券查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/couponList?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10&couponLevel=2"
```

### 会员代金券查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/voucherList?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10&voucherType=1"
```

### 会员退费订单查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/refundList?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10"
```

### 用户行为记录信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/userBehavior?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10"
```

### 用户播放记录信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/member/playHistory?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10&date=2024.01.01"
```

### 文件上传签名获取请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/upload/sign?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&base64=iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mP8/5+hHgAHggJ/PchI7wAAAABJRU5ErkJggg==&fileSize=1024&fileMd5=d41d8cd98f00b204e9800998ecf8427e&fileSubfix=jpg&platName=mgtv"
```

### 文件访问权限设置请求示例

```bash
curl -X POST "http://api.example.com/api/thirdparty/upload/setAcl" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&id=12345&platName=mgtv"
```

### 字典列表查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/callcenter/dictList?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&groupCode=PROBLEM_TYPE&name=技术&isShowDisabled=false"
```

### 第三方管理员登录请求示例

```bash
curl -X POST "http://api.example.com/api/thirdparty/admin/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&email=admin@mgtv.com"
```

### 第三方 CRM 查询工具首页请求示例

```bash
curl -X POST "http://api.example.com/api/thirdparty/support/index" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "appid=your_app_id&sign=your_signature&timestamp=1640995200000&ottExtend=extend_info&saasType=1&otherInfo={\"uuid\":\"user123\",\"company_id\":\"1\",\"chat_id\":\"chat123\"}"
```

### 第三方 CRM 呼叫中心工具首页请求示例

```bash
curl -X POST "http://api.example.com/api/thirdparty/support/callIndex" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123"
```

### 第三方用户行为记录信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/support/userBehavior?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10"
```

### 第三方用户播放记录信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/support/playHistory?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123&page=1&pageSize=10&date=2024.01.01"
```

### 第三方用户信息查询请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/user/info?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123"
```

### 第三方用户重置请求示例

```bash
curl -X GET "http://api.example.com/api/thirdparty/user/reset?appid=your_app_id&sign=your_signature&timestamp=1640995200000&uuid=user123"
```

## 状态码说明

### 工单状态码

- `1`: 创建 - 工单已创建，等待处理
- `2`: 处理中 - 工单正在被处理
- `3`: 结案 - 工单已完成处理

### 坐席状态码

- `ONLINE`: 在线 - 坐席可接听电话
- `OFFLINE`: 离线 - 坐席不可接听电话
- `BUSY`: 忙碌 - 坐席正在处理其他事务

### 通话类型

- `INBOUND`: 呼入 - 客户主动拨打电话
- `OUTBOUND`: 呼出 - 坐席主动拨打电话

### 小休类型

- `1`: 小休 - 坐席临时休息
- `2`: 就餐 - 坐席用餐时间

## 数据字典

### 平台类型 (clientType)

- `web`: Web 端
- `mobile`: 移动端
- `app`: 手机应用
- `tv`: 电视端
- `pc`: PC 客户端

### 问题来源 (questionFrom)

- `用户反馈`: 用户主动反馈
- `系统检测`: 系统自动检测
- `客服发现`: 客服人员发现
- `第三方转接`: 从其他系统转接

### 设备品牌 (equipmentBrand)

- `Apple`: 苹果
- `Samsung`: 三星
- `Huawei`: 华为
- `Xiaomi`: 小米
- `OPPO`: OPPO
- `vivo`: vivo
- `其他`: 其他品牌

### 网络类型 (network)

- `WiFi`: WiFi 网络
- `4G`: 4G 移动网络
- `5G`: 5G 移动网络
- `3G`: 3G 移动网络
- `2G`: 2G 移动网络

### 会员类型 (memberCate)

- `VIP`: VIP 会员
- `SVIP`: 超级 VIP 会员
- `NORMAL`: 普通会员
- `GUEST`: 游客

### 呼入类型 (callCate)

- `咨询`: 用户咨询
- `投诉`: 用户投诉
- `建议`: 用户建议
- `故障`: 系统故障
- `其他`: 其他类型

### 工单处理类型 (workDoneState)

- `0`: 未处理
- `1`: 已处理
- `2`: 转交处理
- `3`: 关闭工单
- `4`: 重新打开

### 连接方式 (linkMode)

- `电话`: 电话连接
- `在线客服`: 在线客服
- `邮件`: 邮件联系
- `微信`: 微信联系
- `其他`: 其他方式

### 工单备注类型 (type)

- `0`: 新建
- `3`: 再次流转
- `4`: 已解决
- `5`: 备注
- `7`: 修改
- `8`: 重新启用

### 工单备注状态 (status2)

- `0`: 草稿
- `1`: 用户不配合关闭
- `2`: 已解决
- `3`: 待回访
- `4`: 待二线解决
- `5`: 待处理
- `6`: 待业务支撑解决
- `7`: 业务支撑解决
- `8`: 二线解决
- `9`: 回访解决

## 接口调用频率限制

- 用户信息查询: 每分钟最多 100 次
- 短信发送: 每分钟最多 10 次
- 工单提交: 每分钟最多 50 次
- 工单列表查询: 每分钟最多 200 次
- 工单详情查询: 每分钟最多 300 次
- 会员多重身份查询: 每分钟最多 100 次
- 会员订单信息查询: 每分钟最多 150 次
- 会员服务信息查询: 每分钟最多 150 次
- 会员观影券查询: 每分钟最多 200 次
- 会员代金券查询: 每分钟最多 200 次
- 会员退费订单查询: 每分钟最多 100 次
- 用户行为记录查询: 每分钟最多 50 次
- 用户播放记录查询: 每分钟最多 80 次
- 文件上传签名获取: 每分钟最多 20 次
- 文件访问权限设置: 每分钟最多 30 次
- 坐席绑定/解绑: 每分钟最多 30 次
- 字典列表查询: 每分钟最多 100 次
- 第三方管理员登录: 每分钟最多 20 次
- 第三方 CRM 查询工具首页: 每分钟最多 50 次
- 第三方 CRM 呼叫中心工具首页: 每分钟最多 50 次
- 第三方用户行为记录查询: 每分钟最多 100 次
- 第三方用户播放记录查询: 每分钟最多 100 次
- 第三方用户信息查询: 每分钟最多 200 次
- 第三方用户重置: 每分钟最多 10 次

## 安全说明

1. **签名验证**: 所有请求必须包含有效的签名，签名算法为 MD5(appid + timestamp + secret)
2. **时间戳验证**: 请求时间戳与服务器时间差不能超过 5 分钟
3. **IP 白名单**: 建议配置调用方 IP 白名单
4. **HTTPS**: 生产环境必须使用 HTTPS 协议
5. **参数加密**: 敏感参数建议进行加密传输

## 常见问题

### Q1: 签名验证失败怎么办？

A1: 检查 appid、timestamp、secret 是否正确，确保签名算法一致。

### Q2: 工单提交失败的原因？

A2: 检查必填参数是否完整，工单分类 ID 是否有效，时间格式是否正确。

### Q3: 文件上传失败怎么办？

A3: 确认上传授权是否有效，文件大小是否超限，文件类型是否支持。

### Q4: 坐席绑定失败的原因？

A4: 检查工号和分机号是否已被其他坐席使用，坐席状态是否正常。

### Q5: 如何获取最新的接口文档？

A5: 请联系技术支持团队获取最新的接口文档和更新说明。

### Q6: 工单列表查询返回空数据怎么办？

A6: 检查访客电话号码是否正确，确认该电话号码下是否有工单记录，检查商户 ID 是否匹配。

### Q7: 工单详情查询失败的原因？

A7: 检查工单 ID 是否存在且有效，确认是否有权限访问该工单信息。

### Q8: 分页查询的注意事项？

A8: page 参数从 1 开始，pageSize 建议不超过 100，避免单次查询数据量过大影响性能。

### Q9: 会员信息查询返回空数据怎么办？

A9: 检查用户 UUID 是否正确，确认该用户是否存在会员信息，检查设备 ID 是否匹配。

### Q10: 会员订单查询失败的原因？

A10: 检查用户 UUID 是否存在，确认是否有权限访问该用户的订单信息。

### Q11: 观影券和代金券的区别？

A11: 观影券用于观看特定内容，代金券用于抵扣消费金额。观影券按次数使用，代金券按金额使用。

### Q12: 会员服务状态说明？

A12: 1-正常：服务正常使用；2-暂停：服务暂时暂停；3-已过期：服务已过期不可使用。

### Q13: 用户行为记录查询返回空数据怎么办？

A13: 检查用户 UUID 是否正确，确认该用户是否有行为记录，检查查询时间范围是否合适。

### Q14: 用户行为记录中的时间戳如何转换？

A14: btime 和 ttime 字段为 Unix 时间戳，可通过 JavaScript 的 new Date(timestamp \* 1000) 转换为可读时间。

### Q15: 用户行为记录的数据来源？

A15: 数据来源于 Elasticsearch 中的 passport_acs 索引，记录用户的登录、观看、搜索等行为。

### Q16: 用户播放记录查询返回空数据怎么办？

A16: 检查用户 UUID 是否正确，确认该用户在指定日期是否有播放记录，检查日期格式是否正确。

### Q17: 播放记录中的操作类型说明？

A17: authPlay-点播播放，authLive-直播播放，authPage-页面访问，authPlayOtt-OTT 播放，authPlayNew-新版播放。

### Q18: 播放记录的数据来源？

A18: 数据来源于 Elasticsearch 中的 as_app 索引，记录用户的视频播放、直播观看等行为。

### Q19: 日期参数格式说明？

A19: date 参数格式为 yyyy.MM.dd，如 2024.01.01，如果不传则默认查询当天的记录。

### Q20: 字典列表查询返回空数据怎么办？

A20: 检查字典类型(groupCode)是否正确，确认该字典类型下是否有数据，检查是否设置了正确的过滤条件。

### Q21: 字典列表查询的参数说明？

A21: groupCode 为必填参数，指定要查询的字典类型；name 为可选参数，用于模糊查询字典名称；isShowDisabled 控制是否显示禁用的字典项。

### Q22: 第三方管理员登录接口的使用场景？

A22: 该接口用于第三方系统获取管理员登录 token，完全信任第三方调用方。通过邮箱地址获取对应客服的登录凭证，用于后续的管理操作。

### Q23: 第三方 CRM 查询工具首页接口的 saasType 参数作用？

A23: saasType 参数控制返回内容类型。当 saasType=1 时，返回小芒 SaaS 系统的 iframe 页面；当 saasType=0 时，返回标准的 CRM 查询工具模板页面。

### Q24: 第三方用户行为记录和会员用户行为记录的区别？

A24: 第三方用户行为记录接口(/api/thirdparty/support/userBehavior)和会员用户行为记录接口(/api/thirdparty/member/userBehavior)功能相同，都是查询用户行为记录，可根据业务需要选择使用。

### Q25: 第三方用户播放记录和会员用户播放记录的区别？

A25: 第三方用户播放记录接口(/api/thirdparty/support/playHistory)和会员用户播放记录接口(/api/thirdparty/member/playHistory)功能相同，都是查询用户播放记录，可根据业务需要选择使用。

### Q26: 第三方用户信息查询返回的数据结构说明？

A26: 该接口返回包含 userInfo(用户基本信息)和 userLogin(用户登录记录)两部分数据，提供用户的完整信息视图。

### Q27: 第三方用户重置接口的作用和注意事项？

A27: 该接口用于重置用户信息，包括密码、登录状态、会话令牌等。重置后用户需要重新登录，请谨慎使用。

### Q28: otherInfo 参数的 JSON 格式说明？

A28: otherInfo 参数需要传递 JSON 格式的字符串，包含 uuid、company_id、chat_id、login_name、goodsId、user_id 等字段，用于小芒 SaaS 系统的参数透传。
