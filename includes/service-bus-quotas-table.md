---
title: include 文件
description: include 文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f48ad6ca74e6ce10148d66549fea16bc74015b2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171218"
---
下表列出了特定于 Azure 服务总线消息传送配额信息。 有关定价的信息和其他服务总线配额，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

| 配额名称 | 范围 | 说明 | 值 |
| --- | --- | --- | --- |
| 每个 Azure 订阅的基本或标准命名空间的最大数目 |命名空间 |通过 Azure 门户将拒绝更多基本或标准命名空间的后续请求。 |100|
| 每个 Azure 订阅的高级命名空间的最大数目 |命名空间 |更多高级命名空间的后续请求会被门户拒绝。 |25 |
| 队列或主题大小 |实体 |创建队列或主题时定义。 <br/><br/> 将拒绝后续的传入消息，且调用代码会收到异常。 |1、2、3、4 GB 或 5 GB。<br /><br />在高级 SKU，并使用标准 SKU[分区](/azure/service-bus-messaging/service-bus-partitioning)启用，最大队列或主题大小是 80 GB。 |
| 命名空间上的并发连接数 |命名空间 |系统会拒绝后续的附加连接请求，且调用代码会收到异常。 REST 操作不计入并发 TCP 连接。 |NetMessaging：1,000.<br /><br />AMQP：5,000. |
| 并发接收队列、 主题或订阅实体上的请求数 |实体 |后续的接收请求被拒绝，并且调用代码会收到异常。 此配额适用于一个主题上所有订阅的并发接收操作总数。 |5,000 |
| 每个命名空间的队列或主题的数量 |命名空间 |系统将拒绝后续的在命名空间中创建新主题或队列的请求。 因此，如果是通过 [Azure 门户][Azure portal]配置的，将生成错误消息。 如果是通过管理 API 调用的，调用代码将收到异常。 |基本或标准层为 1000。 主题和队列的命名空间中的总数必须小于或等于 1,000。 <br/><br/>为高级层中，每个消息传送单元 (MU) 为 1000。 最大限制为 4,000。 |
| 数[分区队列或主题](/azure/service-bus-messaging/service-bus-partitioning)每个命名空间 |命名空间 |系统将拒绝后续的在命名空间中创建新分区主题或队列的请求。 因此，如果是通过 [Azure 门户][Azure portal]配置的，将生成错误消息。 如果是通过管理 API，异常调用**QuotaExceededException**调用代码会收到。 |基本和标准层：100。<br/><br/>中不受支持分区的实体[高级](../articles/service-bus-messaging/service-bus-premium-messaging.md)层。<br/><br />每个分区的队列或主题计入每个命名空间的 1,000 个实体的配额。 |
| 任一消息实体路径的最大大小：队列或主题 |实体 |- |260 个字符。 |
| 任一消息实体名称的最大大小：命名空间、订阅或订阅规则 |实体 |- |50 个字符。 |
| [消息 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) 的最大大小 | 实体 |- | 128 |
| 最大消息大小[会话 ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | 实体 |- | 128 |
| 队列、 主题或订阅实体的消息大小 |实体 |将拒绝超过这些配额的传入消息，且调用代码会收到异常。 |最大消息大小：256 KB[标准层](../articles/service-bus-messaging/service-bus-premium-messaging.md)，为 1MB[高级层](../articles/service-bus-messaging/service-bus-premium-messaging.md)。 <br /><br />由于系统开销，此限制小于这些值。<br /><br />最大标头大小：64 KB。<br /><br />属性包中的标头属性的最大数目：**字节/int。MaxValue**。<br /><br />属性包中属性的最大大小：没有明确限制。 受最大标头大小限制。 |
| 消息队列、 主题或订阅实体的属性大小 |实体 | 异常**SerializationException**生成。 |每个属性的最大消息属性大小为 32,000。 所有属性的累计大小不能超过 64000。 此限制适用于整个标头[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)，其中具有用户属性和系统属性，如[SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)，[标签](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)，并且[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)。 |
| 每个主题的订阅数 |实体 |系统将拒绝后续的为主题创建更多订阅的请求。 因此，如果是通过门户配置的，会显示错误消息。 如果是通过管理 API 调用的，调用代码将收到异常。 |标准和高级层：每个订阅计入 1,000 个实体的配额，即队列、 主题和订阅，每个命名空间。 |
| 每个主题的 SQL 筛选器数 |实体 |本主题的更多筛选器中创建的后续请求将拒绝，且调用代码会收到异常。 |2,000 |
| 每个主题的相关性筛选器数 |实体 |本主题的更多筛选器中创建的后续请求将拒绝，且调用代码会收到异常。 |100,000 |
| SQL 筛选器或操作的大小 |命名空间 |将拒绝更多筛选器中创建的后续请求，且调用代码会收到异常。 |筛选器条件字符串的最大长度：1024 (1K)。<br /><br />规则操作字符串的最大长度：1024 (1K)。<br /><br />每个规则操作的最大表达式数：32. |
| 每个命名空间、队列或主题的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 规则数 |实体、命名空间 |将拒绝后续的创建附加规则请求，且调用代码会收到异常。 |最大规则数：12. <br /><br /> 在服务总线命名空间中配置的规则适用于该命名空间中的所有队列和主题。 |
| 每个事务的消息数 | 事务 | 系统将拒绝其他传入消息，并收到如下异常消息"无法发送超过 100 个消息在单个事务中"时调用的代码。 | 100 <br /><br /> 适用于 Send() 和 SendAsync() 操作。 |

[Azure portal]: https://portal.azure.com
