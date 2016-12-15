---
title: "在逻辑应用中添加 Twilio 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Twilio 连接器概述"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0999d96b4819d6bf03faa843750c5c4b19649774


---
# <a name="get-started-with-the-twilio-connector"></a>Twilio 连接器入门
连接到 Twilio 以发送和接收全球短信、彩信和 IP 消息。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

借助 Twilio，可以：

* 根据从 Twilio 中获取的数据生成业务流。 
* 使用获取消息、列出消息等的操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，收到新 Twilio 消息时，可接受此消息并在服务总线工作流中使用它。 

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Twilio 连接器包括以下操作。 没有任何触发器。 

| 触发器 | 操作 |
| --- | --- |
| 无 |<ul><li>获取消息</li><li>列出消息</li><li>发送消息</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。 

## <a name="create-a-connection-to-twilio"></a>创建到 Twilio 的连接
将此连接器添加到逻辑应用时，输入以下 Twilio 值：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 帐户 ID |是 |输入 Twilio 帐户 ID |
| 访问令牌 |是 |输入 Twilio 访问令牌 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

如果没有，请参阅 [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) 创建访问令牌。

> [!TIP]
> 可在其他逻辑应用中使用此相同 Twilio 连接。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
#### <a name="this-documentation-is-for-version-10"></a>此文档用于版本：1.0
### <a name="get-message"></a>获取消息
返回按提供的消息 ID 指定的单个消息。  
```GET: /Messages/{MessageId}.json```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| MessageId |字符串 |是 |路径 |无 |消息 ID |

### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 404 |找不到消息 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

### <a name="list-messages"></a>列出消息
返回与帐户关联的消息列表。  
```GET: /Messages.json```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 如果 |字符串 |否 |查询 |无 |到电话号码 |
| 从 |字符串 |否 |查询 |无 |从电话号码 |
| DateSent |字符串 |否 |查询 |无 |仅显示此日期发送的消息（以 GMT 格式），如 YYYY-MM-DD 所示。 示例：DateSent=2009-07-06. 还可以指定不等式，例如 DateSent<=YYYY-MM-DD 表示在某个日期当天或午夜之前发送的消息，DateSent>=YYYY-MM-DD 表示在某个日期当天或午夜之后发送的消息。 |
| PageSize |integer |否 |查询 |50 |要在每个列表页中返回多少资源。 默认值为 50。 |
| Page |integer |否 |查询 |0 |页码。 默认值为 0。 |

### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

### <a name="send-message"></a>发送消息
向移动电话号码发送新消息。  
```POST: /Messages.json```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |是 |body |无 |要发送的消息 |

### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest：发送消息操作的请求模型
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| from |字符串 |是 |
| to |字符串 |是 |
| body |字符串 |是 |
| media_url |数组 |否 |
| status_callback |字符串 |否 |
| messaging_service_sid |字符串 |否 |
| application_sid |字符串 |否 |
| max_price |字符串 |否 |

#### <a name="message-model-for-message"></a>消息：消息的模型
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| body |字符串 |否 |
| from |字符串 |否 |
| to |字符串 |否 |
| status |字符串 |否 |
| sid |字符串 |否 |
| account_sid |字符串 |否 |
| api_version |字符串 |否 |
| num_segments |字符串 |否 |
| num_media |字符串 |否 |
| date_created |字符串 |否 |
| date_sent |字符串 |否 |
| date_updated |字符串 |否 |
| direction |字符串 |否 |
| error_code |字符串 |否 |
| error_message |字符串 |否 |
| price |字符串 |否 |
| price_unit |字符串 |否 |
| uri |字符串 |否 |
| subresource_uris |数组 |否 |
| messaging_service_sid |字符串 |否 |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList：列出消息操作的响应模型
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| messages |数组 |否 |
| 页面 |integer |否 |
| page_size |integer |否 |
| num_pages |integer |否 |
| uri |字符串 |否 |
| first_page_uri |字符串 |否 |
| next_page_uri |字符串 |否 |
| total |integer |否 |
| previous_page_uri |字符串 |否 |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList：列出消息操作的响应模型
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| incoming_phone_numbers |数组 |否 |
| 页面 |integer |否 |
| page_size |integer |否 |
| num_pages |integer |否 |
| uri |字符串 |否 |
| first_page_uri |字符串 |否 |
| next_page_uri |字符串 |否 |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest：添加来电号码操作的请求模型
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| PhoneNumber |字符串 |是 |
| AreaCode |字符串 |否 |
| FriendlyName |字符串 |否 |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber：来电号码
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| phone_number |字符串 |否 |
| friendly_name |字符串 |否 |
| sid |字符串 |否 |
| account_sid |字符串 |否 |
| date_created |字符串 |否 |
| date_updated |字符串 |否 |
| capabilities |未定义 |否 |
| status_callback |字符串 |否 |
| status_callback_method |字符串 |否 |
| api_version |字符串 |否 |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities：电话号码功能
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| mms |布尔值 |否 |
| sms |布尔值 |否 |
| voice |布尔值 |否 |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers：可用电话号码
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| phone_number |字符串 |否 |
| friendly_name |字符串 |否 |
| lata |字符串 |否 |
| latitude |字符串 |否 |
| longitude |字符串 |否 |
| postal_code |字符串 |否 |
| rate_center |字符串 |否 |
| region |字符串 |否 |
| MMS |布尔值 |否 |
| SMS |布尔值 |否 |
| voice |布尔值 |否 |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords：用法记录类
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| category |字符串 |否 |
| usage |字符串 |否 |
| usage_unit |字符串 |否 |
| description |字符串 |否 |
| price |数字 |否 |
| price_unit |字符串 |否 |
| count |字符串 |否 |
| count_unit |字符串 |否 |
| start_date |字符串 |否 |
| end_date |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


