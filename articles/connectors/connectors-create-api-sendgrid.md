---
title: SendGrid | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 SendGrid Connection 提供程序可使你发送电子邮件和管理收件人列表。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad35f6590a39972ec9be7168059738b6daae0574


---
# <a name="get-started-with-the-sendgrid-connector"></a>SendGrid 连接器入门
SendGrid Connection 提供程序可使你发送电子邮件和管理收件人列表。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。 
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
SendGrid 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。 

 SendGrid 连接器具有以下可用操作。 没有任何触发器。

### <a name="sendgrid-actions"></a>SendGrid 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |使用 SendGrid API 发送电子邮件（仅限 10000 个收件人） |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |将单个收件人添加到收件人列表 |

## <a name="create-a-connection-to-sendgrid"></a>创建到 SendGrid 的连接
若要使用 SendGrid 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| ApiKey |是 |提供 SendGrid Api 密钥 |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

创建连接后，可使用它执行操作并侦听触发器，如本文所述。

## <a name="reference-for-sendgrid"></a>SendGrid 的参考
适用于版本：1.0

## <a name="sendemail"></a>SendEmail
发送电子邮件：使用 SendGrid API 发送电子邮件（仅限 10,000 个收件人） 

```POST: /api/mail.send.json``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 请求 | |是 |body |无 |要发送的电子邮件 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 429 |请求过多 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="addrecipienttolist"></a>AddRecipientToList
将收件人添加到列表：将单个收件人添加到收件人列表 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| listId |字符串 |是 |路径 |无 |收件人列表的唯一 ID |
| recipientId |字符串 |是 |路径 |无 |收件人的唯一 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
### <a name="emailrequest"></a>EmailRequest
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| from |字符串 |是 |
| fromname |字符串 |否 |
| to |字符串 |是 |
| toname |字符串 |否 |
| subject |字符串 |是 |
| body |字符串 |是 |
| ishtml |布尔值 |否 |
| cc |字符串 |否 |
| ccname |字符串 |否 |
| bcc |字符串 |否 |
| bccname |字符串 |否 |
| replyto |字符串 |否 |
| 日期 |字符串 |否 |
| headers |字符串 |否 |
| 文件 |数组 |否 |
| filenames |数组 |否 |

### <a name="emailresponse"></a>EmailResponse
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| message |字符串 |否 |

### <a name="recipientlists"></a>RecipientLists
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| lists |数组 |否 |

### <a name="recipientlist"></a>RecipientList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| 名称 |字符串 |否 |
| recipient_count |integer |否 |

### <a name="recipients"></a>Recipients
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| recipients |数组 |否 |

### <a name="recipient"></a>Recipient
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| email |字符串 |否 |
| last_name |字符串 |否 |
| first_name |字符串 |否 |
| id |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


