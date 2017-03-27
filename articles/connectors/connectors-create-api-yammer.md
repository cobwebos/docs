---
title: "在逻辑应用中添加 Yammer 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Yammer 连接器概述"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: c69f7af9f9427eb295481657eaefcdad76b48ccb
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-yammer-connector"></a>Yammer 连接器入门
连接到 Yammer 以在企业网络中访问对话。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

借助 Yammer，可以：

* 根据从 Facebook 中获取的数据生成你的业务流。 
* 针对组或关注的源中有新消息的情况使用触发器。
* 使用操作发布消息、获取所有消息等。 这些操作可获得响应，然后使输出可用于其他操作。 例如，出现新消息时，可使用 Office 365 发送电子邮件。

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Yammer 包括以下触发器和操作。 

| 触发器 | 操作 |
| --- | --- |
| <ul><li>组中有新消息时</li><li>我关注的源中有新消息时</li></ul> |<ul><li>获取所有消息</li><li>获取组中的消息</li><li>从我关注的源获取消息</li><li>发布消息</li><li>组中有新消息时</li><li>我关注的源中有新消息时</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。 

## <a name="create-a-connection-to-yammer"></a>创建到 Yammer 的连接
若要使用 Yammer 连接器，首先创建**连接**，然后为以下属性提供详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Yammer 凭据 |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="yammer-rest-api-reference"></a>Yammer REST API 参考
此文档用于版本：1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>获取已登录用户的 Yammer 网络中的所有公共消息
对应于 Yammer Web 界面中的“所有”对话。  
```GET: /messages.json```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| older_then |integer |否 |查询 |无 |返回早于指定为数值字符串形式的消息 ID 的消息。 这对于为消息标页码很有用。 例如，如果当前正在查看 20 条消息，并且最早的是编号 2912，可将“?older_than=2912”添加到请求以获取正在查看的消息之前的 20 条消息。 |
| newer_then |integer |否 |查询 |无 |返回早于指定为数值字符串形式的消息 ID 的消息。 这应在轮询新消息时使用。 如果正在查看消息，并且返回的最新消息为 3516，则可以使用参数“?newer_than=3516”发出请求，以确保不会得到页面上已有消息的重复副本。 |
| limit |integer |否 |查询 |无 |仅返回指定数量的消息。 |
| 页面 |integer |否 |查询 |无 |获取指定的页面。 如果返回的数据大于限制，可以使用此字段访问后续页面 |

### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 408 |请求超时 |
| 429 |请求过多 |
| 500 |内部服务器错误。 发生未知错误 |
| 503 |Yammer 服务不可用 |
| 504 |网关超时 |
| default |操作失败。 |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>向组或所有公司源发布消息
如果提供了组 ID，将向指定组发送消息，否则将在所有公司源中发布它。    
```POST: /messages.json``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| input | |是 |body |无 |发布消息请求 |

### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 201 |已创建 |

## <a name="object-definitions"></a>对象定义
#### <a name="message-yammer-message"></a>消息：Yammer 消息
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| content_excerpt |字符串 |否 |
| sender_id |integer |否 |
| replied_to_id |integer |否 |
| created_at |字符串 |否 |
| network_id |integer |否 |
| message_type |字符串 |否 |
| sender_type |字符串 |否 |
| url |字符串 |否 |
| web_url |字符串 |否 |
| group_id |integer |否 |
| body |未定义 |否 |
| thread_id |integer |否 |
| direct_message |布尔值 |否 |
| client_type |字符串 |否 |
| client_url |字符串 |否 |
| 语言 |字符串 |否 |
| notified_user_ids |数组 |否 |
| privacy |字符串 |否 |
| liked_by |未定义 |否 |
| system_message |布尔值 |否 |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest：表示使 Yammer 连接器向 yammer 进行发布的发布请求
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| body |字符串 |是 |
| group_id |integer |否 |
| replied_to_id |integer |否 |
| direct_to_id |integer |否 |
| broadcast |布尔值 |否 |
| topic1 |字符串 |否 |
| topic2 |字符串 |否 |
| topic3 |字符串 |否 |
| topic4 |字符串 |否 |
| topic5 |字符串 |否 |
| topic6 |字符串 |否 |
| topic7 |字符串 |否 |
| topic8 |字符串 |否 |
| topic9 |字符串 |否 |
| topic10 |字符串 |否 |
| topic11 |字符串 |否 |
| topic12 |字符串 |否 |
| topic13 |字符串 |否 |
| topic14 |字符串 |否 |
| topic15 |字符串 |否 |
| topic16 |字符串 |否 |
| topic17 |字符串 |否 |
| topic18 |字符串 |否 |
| topic19 |字符串 |否 |
| topic20 |字符串 |否 |

#### <a name="messagelist-list-of-messages"></a>MessageList：消息列表
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| messages |数组 |否 |

#### <a name="messagebody-message-body"></a>MessageBody：消息正文
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| parsed |字符串 |否 |
| plain |字符串 |否 |
| rich |字符串 |否 |

#### <a name="likedby-liked-by"></a>LikedBy：赞者
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| count |integer |否 |
| names |数组 |否 |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity：赞者
| Name | 数据类型 | 必选 |
| --- | --- | --- |
| type |字符串 |否 |
| id |integer |否 |
| full_name |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png

