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
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77651583"
---
下表列出了特定于 Azure 服务总线消息传送的配额信息。 有关服务总线的定价和其他配额的信息，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

| 配额名称 | 作用域 | 注释 | 值 |
| --- | --- | --- | --- |
| 每个 Azure 订阅的基本或标准命名空间的最大数目 |命名空间 |其他基本或标准命名空间的后续请求会被 Azure 门户拒绝。 |100|
| 每个 Azure 订阅的高级命名空间的最大数量 |命名空间 |其他高级命名空间的后续请求会被门户拒绝。 |100 |
| 队列或主题大小 |实体 |在创建队列或主题时定义。 <br/><br/> 后续传入的消息将被拒绝，并且调用代码将收到异常。 |1、2、3、4 GB 或 5 GB。<br /><br />在高级 SKU 和启用[分区](/azure/service-bus-messaging/service-bus-partitioning)的标准 sku 中，最大队列或主题大小为 80 GB。 |
| 命名空间上的并发连接数 |命名空间 |系统会拒绝后续的附加连接请求，且调用代码会收到异常。 REST 操作不计入并发 TCP 连接。 |NetMessaging：1000。<br /><br />AMQP：5000。 |
| 队列、主题或订阅实体上的并发接收请求数 |实体 |将拒绝后续的接收请求，且调用代码会收到异常。 此配额适用于一个主题上所有订阅的并发接收操作总数。 |5,000 |
| 每个命名空间的主题或队列数 |命名空间 |系统将拒绝后续的在命名空间中创建新主题或队列的请求。 因此，如果是通过 [Azure 门户][Azure portal]配置的，将生成错误消息。 如果是通过管理 API 调用的，调用代码将收到异常。 |"基本" 或 "标准" 级别为10000。 命名空间中主题和队列的数目之和必须小于或等于 10,000。 <br/><br/>对于高级层，每个消息传送单元（MU）1000。 最大限制为4000。 |
| 每个命名空间的[分区主题或队列](/azure/service-bus-messaging/service-bus-partitioning)数 |命名空间 |系统将拒绝后续的在命名空间中创建新分区主题或队列的请求。 因此，如果是通过 [Azure 门户][Azure portal]配置的，将生成错误消息。 如果是从管理 API 调用的，则调用代码将收到异常**QuotaExceededException** 。 |基本层和标准层：100。<br/><br/>[高级](../articles/service-bus-messaging/service-bus-premium-messaging.md)层不支持已分区的实体。<br/><br />每个分区的队列或主题都将计入每个命名空间1000个实体的配额。 |
| 任一消息实体路径的最大大小：队列或主题 |实体 |- |260个字符。 |
| 任一消息实体名称的最大大小：命名空间、订阅或订阅规则 |实体 |- |50个字符。 |
| [消息 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) 的最大大小 | 实体 |- | 128 |
| 消息[会话 ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid)的最大大小 | 实体 |- | 128 |
| 队列、主题或订阅实体的消息大小 |实体 |将拒绝超过这些配额的传入消息，并且调用代码将收到异常。 |最大消息大小：[标准级别](../articles/service-bus-messaging/service-bus-premium-messaging.md)为 256 KB，对于[高级层](../articles/service-bus-messaging/service-bus-premium-messaging.md)，为 1 MB。 <br /><br />由于系统开销，此限制小于这些值。<br /><br />最大标头大小： 64 KB。<br /><br />属性包中的最大标头属性数： **byte/int。** 个。<br /><br />属性包中属性的最大大小：没有明确的限制。 受最大标头大小限制。 |
| 队列、主题或订阅实体的消息属性大小 |实体 | 生成异常**SerializationException** 。 |每个属性的最大消息属性大小为32000。 所有属性的累计大小不能超过64000。 此限制适用于[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)的整个标头，其中包含用户属性和系统属性，例如[SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)、 [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)和[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)。 |
| 每个主题的订阅数 |实体 |系统将拒绝后续的为主题创建更多订阅的请求。 因此，如果是通过门户配置的，会显示错误消息。 如果是通过管理 API 调用的，调用代码将收到异常。 |标准级别为每个主题2000。 |
| 每个主题的 SQL 筛选器数 |实体 |将拒绝后续的在主题中创建附加筛选器的请求，且调用代码会收到异常。 |2,000 |
| 每个主题的相关性筛选器数 |实体 |将拒绝后续的在主题中创建附加筛选器的请求，且调用代码会收到异常。 |100,000 |
| SQL 筛选器或操作的大小 |命名空间 |将拒绝后续的创建附加筛选器请求，且调用代码会收到异常。 |筛选条件字符串的最大长度：1024（1 K）。<br /><br />规则操作字符串的最大长度：1024（1 K）。<br /><br />每个规则操作的最大表达式数：32。 |
| 每个命名空间、队列或主题的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 规则数 |实体、命名空间 |将拒绝后续的创建附加规则的请求，且调用代码会收到异常。 |每个实体类型的最大规则数：12。 <br /><br /> 在服务总线命名空间上配置的规则适用于所有类型：队列和主题。 |
| 每个事务的消息数 | 事务 | 将拒绝其他传入消息，并且调用代码将收到指示 "无法在单个事务中发送超过100条消息" 的异常。 | 100 <br /><br /> 适用于 Send() 和 SendAsync() 操作********。 |
| 虚拟网络和 IP 筛选器规则的数量 | 命名空间 | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
