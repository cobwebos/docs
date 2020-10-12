---
title: Azure 服务总线中的 AMQP 1.0 请求/响应操作
description: 本文定义了 Microsoft Azure 服务总线中基于 AMQP 请求/响应的操作列表。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064408"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Microsoft Azure 服务总线：基于请求-响应的操作中的 AMQP 1.0

本文定义 Microsoft Azure 服务总线基于请求/响应的操作的列表。 此信息基于 AMQP 管理版本 1.0 工作草案。  
  
有关详细的线级 AMQP 1.0 协议指南（其中介绍了如何基于 OASIS AMQP 技术规范实现和建立服务总线），请参阅 [Azure 服务总线和事件中心的 AMQP 1.0 协议指南][AMQP 1.0 协议指南]。  
  
## <a name="concepts"></a>概念  
  
### <a name="entity-description"></a>实体描述  

实体说明是指服务总线的 [QueueDescription 类](/dotnet/api/microsoft.servicebus.messaging.queuedescription)、[TopicDescription 类](/dotnet/api/microsoft.servicebus.messaging.topicdescription)或 [SubscriptionDescription 类](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)对象。  
  
### <a name="brokered-message"></a>中转消息  

表示映射到 AMQP 消息的服务总线中的消息。 [服务总线 AMQP 协议指南](service-bus-amqp-protocol-guide.md)中定义了映射。  
  
## <a name="attach-to-entity-management-node"></a>附加到实体管理节点  

本文档中所述的全部操作都遵循请求/响应模式，这些操作的范围限制到实体，并需要附加到实体管理节点。  
  
### <a name="create-link-for-sending-requests"></a>创建用于发送请求的链接  

创建用于发送请求的管理节点链接。  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>创建用于接收响应的链接  

创建用于从管理节点接收响应的链接。  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>传输请求消息  

传输请求消息。  
对于支持事务的操作，可以选择添加事务状态。

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
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

必须如下所示处理服务总线实体：  
  
|实体类型|地址|示例|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|主题|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|订阅|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>消息操作  
  
### <a name="message-renew-lock"></a>消息续订锁  

按实体描述中指定的时间延长消息锁。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
 请求消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid 的数组|是|要续订的消息锁令牌。|  

> [!NOTE]
> 锁定标记是收到消息的 `DeliveryTag` 属性。 请参阅以下使用 [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) 检索这些标记的示例。 该标记也可能以“x-opt-lock-token”的形式出现在“DeliveryAnnotations”中，但这不能得到保证，`DeliveryTag` 应该是首选。 
> 
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败。|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 amqp-value 部分，其中所含映射必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|expirations|时间戳的数组|是|对应于请求锁令牌的消息锁令牌新到期日。|  
  
### <a name="peek-message"></a>速览消息  

在不锁定的情况下速览消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|是|从其开始速览的序列号。|  
|`message-count`|int|是|要速览的消息数量的上限。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 204: 无内容 - 没有更多消息|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|消息列表，其中每个映射表示一条消息。|  
  
代表消息的映射必须包含以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|message|字节的数组|是|AMQP 1.0 连线编码消息。|  
  
### <a name="schedule-message"></a>计划消息  

计划消息。 此操作支持事务。
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|消息列表，其中每个映射表示一条消息。|  
  
代表消息的映射必须包含以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|message-id|string|是|`amqpMessage.Properties.MessageId` 为字符串|  
|session-id|string|否|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|否|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|string|否|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|字节的数组|是|AMQP 1.0 连线编码消息。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败。|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含映射必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|长整数的数组|是|计划消息的序列号。 序列号用于取消。|  
  
### <a name="cancel-scheduled-message"></a>取消计划消息  

取消计划消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|长整数的数组|是|要取消的计划消息的序列号。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败。|  
|statusDescription|string|否|状态的说明。|   
  
## <a name="session-operations"></a>会话操作  
  
### <a name="session-renew-lock"></a>会话续订锁  

按实体描述中指定的时间延长消息锁。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|是|会话 ID。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 204: 无内容 - 没有更多消息|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含映射必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|是|新到期日。|  
  
### <a name="peek-session-message"></a>速览会话消息  

在不锁定的情况下速览会话消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|是|从其开始速览的序列号。|  
|message-count|int|是|要速览的消息数量的上限。|  
|session-id|string|是|会话 ID。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 204: 无内容 - 没有更多消息|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含映射必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|消息列表，其中每个映射表示一条消息。|  
  
 代表消息的映射必须包含以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|message|字节的数组|是|AMQP 1.0 连线编码消息。|  
  
### <a name="set-session-state"></a>设置会话状态  

设置会话的状态。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|是|会话 ID。|  
|session-state|字节的数组|是|不透明的二进制数据。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|string|否|状态的说明。|  
  
### <a name="get-session-state"></a>获取会话状态  

获取会话的状态。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|session-id|string|是|会话 ID。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|session-state|字节的数组|是|不透明的二进制数据。|  
  
### <a name="enumerate-sessions"></a>枚举会话  

消息传送实体上的枚举会话。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|是|筛选，以便仅包含在给定时间后更新的会话。|  
|skip|int|是|跳过会话数量。|  
|top|int|是|会话数量的上限。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 有更多消息<br /><br /> 204: 无内容 - 没有更多消息|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|skip|int|是|状态代码为 200 时的已跳过会话的数量。|  
|sessions-ids|字符串的数组|是|状态代码为 200 时的会话 ID 的数组。|  
  
## <a name="rule-operations"></a>规则操作  
  
### <a name="add-rule"></a>添加规则  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|是|规则名称，不包括订阅和主题名称。|  
|rule-description|map|是|如下一部分中指定的规则描述。|  
  
**rule-description** 映射必须包含以下条目，其中 **sql-filter** 和 **correlation-filter** 互斥：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|是|`sql-filter`，如下一部分中所指定。|  
|correlation-filter|map|是|`correlation-filter`，如下一部分中所指定。|  
|sql-rule-action|map|是|`sql-rule-action`，如下一部分中所指定。|  
  
sql-filter 映射必须包含以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|expression|string|是|Sql 筛选器表达式。|  
  
**correlation-filter** 映射至少必须包含以下条目之一：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|否||  
|message-id|string|否||  
|to|string|否||  
|reply-to|string|否||  
|label|string|否||  
|session-id|string|否||  
|reply-to-session-id|string|否||  
|content-type|string|否||  
|properties|map|否|映射到服务总线 [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)。|  
  
**sql-rule-action** 映射必须包含以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|expression|string|是|Sql 操作表达式。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|string|否|状态的说明。|  
  
### <a name="remove-rule"></a>删除规则  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|是|规则名称，不包括订阅和主题名称。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|string|否|状态的说明。|  
  
### <a name="get-rules"></a>获取规则

#### <a name="request"></a>请求

请求消息必须包含以下应用程序属性：

|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  

请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|top|int|是|要在页面中提取的规则数量。|  
|skip|int|是|要跳过的规则数量。 定义规则列表中的起始索引 (+1)。 | 

#### <a name="response"></a>响应

响应消息包含以下属性：

|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|规则| 映射数组|是|规则数组。 每个规则均由一个映射表示。|

数组中的每个映射条目都包含以下属性：

|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|rule-description|所描述对象的数组|是|带有 AMQP 的 `com.microsoft:rule-description:list` 描述了代码 0x0000013700000004| 

`com.microsoft.rule-description:list` 是所描述对象的数组。 此数组包括以下内容：

|索引|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
| 0 | 所描述对象的数组 | 是 | `filter` 如下所述。 |
| 1 | 所描述对象的数组 | 是 | `ruleAction` 如下所述。 |
| 2 | string | 是 | 规则名称。 |

`filter` 可以是以下任一类型：

| 描述符名称 | 描述符代码 | Value |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL 筛选器 |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | 关联筛选器 |
| `com.microsoft:true-filter:list` | 0x000001370000007 | True 筛选器表示 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | False 筛选器表示 1=0 |

`com.microsoft:sql-filter:list` 是描述数组，它包括：

|索引|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
| 0 | string | 是 | SQL 筛选表达式 |

`com.microsoft:correlation-filter:list` 是描述数组，它包括：

|索引（如果存在）|值类型|值内容|  
|---------|----------------|--------------|
| 0 | string | 相关性 ID |
| 1 | string | 消息 ID |
| 2 | string | 如果 |
| 3 | string | 回复 |
| 4 | string | Label |
| 5 | string | 会话 ID |
| 6 | string | 会话 ID 回复|
| 7 | string | 内容类型 |
| 8 | 映射 | 定义属性的应用程序的映射 |

`ruleAction` 可以是以下任一类型：

| 描述符名称 | 描述符代码 | Value |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | 空规则操作 - 不存在任何规则操作 |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL 规则操作 |

`com.microsoft:sql-rule-action:list` 是一个所描述对象的数组，其第一个条目是包含 SQL 规则操作表达式的字符串。

## <a name="deferred-message-operations"></a>延迟的消息操作  
  
### <a name="receive-by-sequence-number"></a>按序列号接收  

按序列号接收延迟的消息。  
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|长整数的数组|是|序列号。|  
|receiver-settle-mode|ubyte|是|AMQP 核心 v1.0 中指定的“接收方结算”  模式。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|string|否|状态的说明。|  
  
响应消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|messages|映射列表|是|消息列表，其中每个映射表示一条消息。|  
  
代表消息的映射必须包含以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|是|`receiver-settle-mode` 为 1 时的锁令牌。|  
|message|字节的数组|是|AMQP 1.0 连线编码消息。|  
  
### <a name="update-disposition-status"></a>更新处置状态  

更新延迟消息的处置状态。 该操作支持事务。
  
#### <a name="request"></a>请求  

请求消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|operation|字符串|是|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|否|操作服务器超时以毫秒为单位。|  
  
请求消息正文必须包含 **amqp-value** 部分，其中所含**映射**必须包括以下条目：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|是|已完成<br /><br /> 已放弃<br /><br /> 已挂起|  
|lock-tokens|uuid 的数组|是|用于更新处置状态的消息锁令牌。|  
|deadletter-reason|string|否|如果处置状态设置为 **已挂起**，可进行设置。|  
|deadletter-description|string|否|如果处置状态设置为 **已挂起**，可进行设置。|  
|properties-to-modify|map|否|要修改的服务总线中转消息属性的列表。|  
  
#### <a name="response"></a>响应  

响应消息必须包含以下应用程序属性：  
  
|键|值类型|必须|值内容|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|是|HTTP 响应代码 [RFC2616]<br /><br /> 200: 正常 – 成功，其他表示失败|  
|statusDescription|string|否|状态的说明。|

## <a name="next-steps"></a>后续步骤

若要详细了解 AMQP 和服务总线，请单击以下链接：

* [服务总线 AMQP 概述]
* [AMQP 1.0 协议指南]
* [适用于 Windows Server 的服务总线中的 AMQP]

[服务总线 AMQP 概述]: service-bus-amqp-overview.md
[AMQP 1.0 协议指南]: service-bus-amqp-protocol-guide.md
[适用于 Windows Server 的服务总线中的 AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)