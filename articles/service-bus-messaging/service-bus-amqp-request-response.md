---
title: "Azure 服务总线基于请求-响应的操作中的 AMQP 1.0 | Microsoft Docs"
description: "Microsoft Azure 服务总线基于请求/响应的操作的列表。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 756565b3da6e0a818d1ee3d5e17f942d96be14f0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---

# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Microsoft Azure 服务总线：基于请求-响应的操作中的 AMQP 1.0

本主题定义 Microsoft Azure 服务总线基于请求/响应的操作的列表。 此信息基于 AMQP 管理版本 1.0 工作草稿。  
  
有关详细的线级 AMQP 1.0 协议指南（其中介绍了如何基于 OASIS AMQP 技术规范实现和建立服务总线），请参阅 [Azure 服务总线和事件中心的 AMQP 1.0 协议指南](service-bus-amqp-protocol-guide.md)。  
  
## <a name="concepts"></a>概念  
  
### <a name="entity-description"></a>实体说明  

实体说明是指服务总线的 [QueueDescription 类](/dotnet/api/microsoft.servicebus.messaging.queuedescription)、[TopicDescription 类](/dotnet/api/microsoft.servicebus.messaging.topicdescription)或 [SubscriptionDescription 类](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)对象。  
  
### <a name="brokered-message"></a>中转消息  

表示映射到 AMQP 消息的服务总线中的消息。 [服务总线 AMQP 协议指南](service-bus-amqp-protocol-guide.md)中定义了映射。  
  
## <a name="attach-to-entity-management-node"></a>连接到实体管理节点  

本文档中描述的所有操作都遵循请求/响应模式，都限于某个实体的范围，且都需要连接到实体管理节点。  
  
### <a name="create-link-for-sending-requests"></a>创建用于发送请求的链接  

创建用于发送请求的管理节点的链接。  
  
```  
requestLink = session.attach(     
role: SENDER,   
    target: { address: "<entity address>/$management" },   
    source: { address: ""<my request link unique address>" }   
)  
  
```  
  
### <a name="create-link-for-receiving-responses"></a>创建用于接收响应的链接  

创建一个链接，以便从管理节点接收响应。  
  
```  
responseLink = session.attach(    
role: RECEIVER,   
    source: { address: "<entity address>/$management" }   
    target: { address: "<my response link unique address>" }   
)  
  
```  
  
### <a name="transfer-a-request-message"></a>传输请求消息  

传输请求消息。  
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
        )  
```  
  
### <a name="receive-a-response-message"></a>接收响应消息  

从响应链接接收响应消息。  
  
```  
responseMessage = responseLink.receiveTransfer()  
```  
  
响应消息采用以下形式：
  
```  
Message(  
properties: {     
        correlation-id: <request id>  
    },  
    application-properties: {  
            "statusCode" -> <status code>,  
            "statusDescription" -> <status description>,  
           },         
)  
  
```  
  
### <a name="service-bus-entity-address"></a>服务总线实体地址  

服务总线实体的地址必须如下所示：  
  
|实体类型|地址|示例|  
|-----------------|-------------|-------------|  
|队列|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|主题|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|订阅|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>消息操作  
  
### <a name="message-renew-lock"></a>消息续订锁定  

按照实体说明中指定的时间延长消息的锁定。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
 请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid 数组|是|用于续订的消息锁定令牌。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败。|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|过期时间|时间戳数组|是|与请求锁定令牌对应的消息锁定令牌的新过期时间。|  
  
### <a name="peek-message"></a>扫视消息  

在不锁定的情况下扫视消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|是|从其开始扫视的序列号。|  
|`message-count`|int|是|要扫视的消息的最大数目。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 0xcc: 无内容 – 没有更多消息|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|映射列表，其中的每个映射都代表一条消息。|  
  
代表消息的映射必须包含以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|message|字节数组|是|AMQP 1.0 有线编码消息。|  
  
### <a name="schedule-message"></a>计划消息  

计划消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|映射列表，其中的每个映射都代表一条消息。|  
  
代表消息的映射必须包含以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|message-id|字符串|是|`amqpMessage.Properties.MessageId`，充当字符串|  
|session-id|字符串|是|`amqpMessage.Properties.GroupId as string`|  
|partition-key|字符串|是|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|message|字节数组|是|AMQP 1.0 有线编码消息。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败。|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 数组|是|计划的消息的序列号。 序列号用于取消操作。|  
  
### <a name="cancel-scheduled-message"></a>取消计划的消息  

取消计划的消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 数组|是|要取消的已计划消息的序列号。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败。|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 数组|是|计划的消息的序列号。 序列号用于取消操作。|  
  
## <a name="session-operations"></a>会话操作  
  
### <a name="session-renew-lock"></a>会话续订锁定  

按照实体说明中指定的时间延长消息的锁定。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|session-id|字符串|是|会话 ID。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 0xcc: 无内容 – 没有更多消息|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|是|新的过期时间。|  
  
### <a name="peek-session-message"></a>扫视会话消息  

在不锁定的情况下扫视会话消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|是|从其开始扫视的序列号。|  
|message-count|int|是|要扫视的消息的最大数目。|  
|session-id|字符串|是|会话 ID。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 0xcc: 无内容 – 没有更多消息|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|映射列表，其中的每个映射都代表一条消息。|  
  
 代表消息的映射必须包含以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|message|字节数组|是|AMQP 1.0 有线编码消息。|  
  
### <a name="set-session-state"></a>设置会话状态  

设置会话的状态。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|session-id|字符串|是|会话 ID。|  
|session-state|字节数组|是|不透明的二进制数据。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|字符串|否|状态的说明。|  
  
### <a name="get-session-state"></a>获取会话状态  

获取会话的状态。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|session-id|字符串|是|会话 ID。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|session-state|字节数组|是|不透明的二进制数据。|  
  
### <a name="enumerate-sessions"></a>枚举会话  

枚举消息传递实体上的会话。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|是|进行筛选，只包括在给定时间后更新的会话。|  
|skip|int|是|跳过多个会话。|  
|top|int|是|最大会话数。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 0xcc: 无内容 – 没有更多消息|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|skip|int|是|状态代码为 200 时跳过的会话数。|  
|sessions-ids|字符串数组|是|状态代码为 200 时的会话 ID 数组。|  
  
## <a name="rule-operations"></a>规则操作  
  
### <a name="add-rule"></a>添加规则  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|字符串|是|规则名称，不包括订阅和主题名称。|  
|rule-description|map|是|规则说明，详见下一部分。|  
  
rule-description 映射必须包含以下条目，其中 sql-filter 和 correlation-filter 互斥：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|是|`sql-filter`，详见下一部分。|  
|correlation-filter|map|是|`correlation-filter`，详见下一部分。|  
|sql-rule-action|map|是|`sql-rule-action`，详见下一部分。|  
  
sql-filter 映射必须包含以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|表达式|字符串|是|Sql 筛选器表达式。|  
  
correlation-filter 映射至少必须包含以下条目之一：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|correlation-id|字符串|否||  
|message-id|字符串|否||  
|to|字符串|否||  
|reply-to|字符串|否||  
|label|字符串|否||  
|session-id|字符串|否||  
|reply-to-session-id|字符串|否||  
|content-type|字符串|否||  
|属性|map|否|映射到服务总线 [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties)。|  
  
sql-rule-action 映射必须包含以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|表达式|字符串|是|Sql 操作表达式。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|字符串|否|状态的说明。|  
  
### <a name="remove-rule"></a>删除规则  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|字符串|是|规则名称，不包括订阅和主题名称。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|字符串|否|状态的说明。|  
  
## <a name="deferred-message-operations"></a>延迟的消息操作  
  
### <a name="receive-by-sequence-number"></a>按序列号接收  

按序列号接收延迟的消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|long 数组|是|序列号。|  
|receiver-settle-mode|ubyte|是|AMQP 核心 v1.0 中指定的“接收方结算”模式。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|字符串|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|映射列表，其中的每个映射都代表一条消息。|  
  
代表消息的映射必须包含以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|是|锁定令牌（如果 `receiver-settle-mode` 为 1）。|  
|message|字节数组|是|AMQP 1.0 有线编码消息。|  
  
### <a name="update-disposition-status"></a>更新处理状态  

更新已延迟消息的处理状态。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|disposition-status|字符串|是|已完成<br /><br /> 已放弃<br /><br /> 已暂停|  
|lock-tokens|uuid 数组|是|消息锁定标记，用于更新处理状态。|  
|deadletter-reason|字符串|否|如果处理状态设置为“已暂停”，则可进行设置。|  
|deadletter-description|字符串|否|如果处理状态设置为“已暂停”，则可进行设置。|  
|properties-to-modify|map|否|要修改的服务总线中转消息属性的列表。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|密钥|值类型|必选|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|字符串|否|状态的说明。|

## <a name="next-steps"></a>后续步骤

若要详细了解 AMQP 和服务总线，请单击以下链接：

* [服务总线 AMQP 概述]
* [针对服务总线分区队列和主题的 AMQP 1.0 支持]
* [适用于 Windows Server 的服务总线中的 AMQP]

[服务总线 AMQP 概述]: service-bus-amqp-overview.md
[针对服务总线分区队列和主题的 AMQP 1.0 支持]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[适用于 Windows Server 的服务总线中的 AMQP]: https://msdn.microsoft.com/library/dn574799.asp
