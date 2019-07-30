---
title: 使 Azure 服务总线应用程序免受服务总线中断和灾难影响 | Microsoft Docs
description: 保护应用程序免受潜在服务总线故障影响的技术。
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24fba1961c8fd95f1b9489716d690dd6eaa97b62
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274849"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>使应用程序免受服务总线中断和灾难影响的最佳实践

任务关键型应用程序必须连续运行，即使是在计划外中断或灾难发生时。 本文介绍可用于保护服务总线应用程序免受潜在的服务中断和灾难影响的技术。

中断定义为 Azure 服务总线暂时不可用。 中断会影响服务总线的一些组件，例如消息存储空间，甚至整个数据中心。 问题解决后，服务总线恢复可用。 通常情况下，中断不会导致消息或其他数据丢失。 组件故障的一个示例是特定的消息存储空间不可用。 数据中心范围中断的示例有数据中心电源故障或数据中心网络交换机故障。 中断可能会持续几分钟到几天的时间。

灾难定义为服务总线缩放单元或数据中心永久丢失。 数据中心可能会也可能不会恢复可用。 通常，灾难将导致消息或其他数据的部分或全部丢失。 灾难的示例包括火灾、洪灾或地震。

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>防范中断和灾难 - 服务总线高级版
高可用性和灾难恢复概念直接内置到 Azure 服务总线高级层中，无论是在同一区域中（通过可用性区域）还是跨不同的区域（通过异地灾难恢复）都可以实现。

### <a name="geo-disaster-recovery"></a>异地灾难恢复

服务总线高级版支持命名空间级别的异地灾难恢复。 有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](service-bus-geo-dr.md)。 灾难恢复功能仅适用于[高级 SKU](service-bus-premium-messaging.md)，可实现元数据灾难恢复，并且依赖于主要和辅助灾难恢复命名空间。

### <a name="availability-zones"></a>可用性区域

服务总线高级 SKU 支持[可用性区域](../availability-zones/az-overview.md)，在同一 Azure 区域内提供故障隔离位置。

> [!NOTE]
> Azure 服务总线高级版的可用性区域支持仅适用于存在可用性区域的 [Azure 区域](../availability-zones/az-overview.md#services-support-by-region)。

可以使用 Azure 门户仅在新的命名空间上启用可用性区域。 服务总线不支持迁移现有命名空间。 在命名空间上启用区域冗余之后，不能将其禁用。

![第][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>防范中断和灾难 - 服务总线标准版
为了在使用标准消息传送定价层时实现针对数据中心中断的恢复，服务总线支持两种方法：主动和被动复制。 对于每一种方法，如果必须在数据中心中断的情况下仍可访问给定的队列或主题，可在两个命名空间中进行创建。 两个实体可以具有相同的名称。 例如，可在 **contosoPrimary.servicebus.windows.net/myQueue** 下访问主要队列，而在 **contosoSecondary.servicebus.windows.net/myQueue** 下访问其辅助队列。

>[!NOTE]
> **主动复制**和**被动复制**设置是常规用途解决方案，不是服务总线的特定功能。 复制逻辑（发送到 2 个不同的命名空间）存在于发送方应用程序上，而接收方必须具有用于检测重复项的自定义逻辑。

如果应用程序不需要发送方到接收方的持续通信，则该应用程序可实施一个用于防止消息丢失的持久客户端队列，从而保护发送方免受任何暂时性服务总线故障的影响。

### <a name="active-replication"></a>主动复制
主动复制对于每个操作都使用这两个命名空间中的实体。 任何发送消息的客户端都将发送同一条消息的两个副本。 将第一个副本发送到主要实体（例如 **contosoPrimary.servicebus.windows.net/sales**），并将该消息的第二个副本发送到辅助实体（例如 **contosoSecondary.servicebus.windows.net/sales**）。

客户端从两个队列接收消息。 如果接收方处理了消息的第一个副本，则第二个副本会被取消。 要取消重复的消息，发送方必须用唯一标识符标记每一条消息。 必须用同一标识符标记消息的两个副本。 可使用 [BrokeredMessage.MessageId][BrokeredMessage.MessageId] 或 [BrokeredMessage.Label][BrokeredMessage.Label] 属性或自定义属性对消息进行标记。 接收方必须保留已接收消息的列表。

[使用服务总线标准级别的异地复制][Geo-replication with Service Bus Standard Tier]示例演示了消息传送实体的主动复制。

> [!NOTE]
> 主动复制方法将使操作数加倍，因此这种方法可能导致成本上升。
> 
> 

### <a name="passive-replication"></a>被动复制
在无故障的情况下，被动复制仅使用两个消息传送实体之一。 客户端将消息发送给活动实体。 如果针对活动实体的操作失败并返回错误代码，表明承载活动实体的数据中心可能不可用，则客户端将该消息的副本发送到备份实体。 此时，活动实体与备份实体互换角色：进行发送的客户端将旧的活动实体认定为新的备份实体，而将旧的备份实体认定为新的活动实体。 如果两次发送操作都失败，则两个实体的角色将保持不变并返回错误。

客户端从两个队列接收消息。 因为接收方可能接收同一条消息的两个副本，所以接收方必须取消重复消息。 可通过与主动复制中所述的相同方式取消重复消息。

一般来说，被动复制比主动重复更经济，因为在大多数情况下仅执行一个操作。 延迟、吞吐量和货币成本均与非复制场景相同。

使用被动复制时，在以下情况下可能丢失消息或接收两次：

* **消息延迟或丢失**：假定发送方将消息 m1 成功发送到主要队列，而该队列在接收方接收 m1 之前变为不可用。 发送方将后续消息 m2 发送给辅助队列。 如果主要队列是暂时不可用，则接收方会在该队列恢复可用后接收 m1。 如果发生灾难，则接收方可能永远无法接收 m1。
* **重复接收**：假定发送方将消息 m 发送给主要队列。 服务总线成功处理了 m 但无法发送响应。 发送操作超时后，发送方将向辅助队列发送 m 的一份相同副本。 如果接收方能够在主要队列变为不可用之前接收 m 的第一个副本，则接收方会在几乎同一时间接收 m 的两个副本。 如果接收方不能在主要队列变为不可用之前接收 m 的第一个副本，则接收方首先仅接收 m 的第二个副本，但会在主要队列变为可用后接收 m 的另一个副本。

[使用服务总线标准级别的异地复制][Geo-replication with Service Bus Standard Tier]示例演示了消息传送实体的被动复制。

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>保护中继终结点免受数据中心中断或灾难的影响
异地复制[Azure 中继](../service-bus-relay/relay-what-is-it.md)终结点允许公开中继终结点可让出现服务总线服务中断的情况下访问的服务。 若要实现异地复制，该服务必须在不同的命名空间中创建两个中继终结点。 命名空间必须位于不同的数据中心，且两个终结点必须具有不同的名称。 例如，可在 **contosoPrimary.servicebus.windows.net/myPrimaryService** 下访问主要终结点，而在 **contosoSecondary.servicebus.windows.net/mySecondaryService** 下访问其辅助终结点。

该服务随后侦听两个终结点，客户端可通过其中任一终结点调用服务。 客户端应用程序随机选取一个中继作为主要终结点，并向活动终结点发送请求。 如果操作失败并返回错误代码，此故障指示中继终结点不可用。 应用程序会打开通向备份终结点的通道并重新发送请求。 此时，活动终结点与备份终结点将互换角色：客户端应用程序会将旧的活动终结点认定为新的备份终结点，而将旧的备份终结点认定为新的活动终结点。 如果两次发送操作都失败，则两个实体的角色将保持不变并返回错误。

## <a name="next-steps"></a>后续步骤
若要了解有关灾难恢复的详细信息，请参阅这些文章：

* [Azure 服务总线异地灾难恢复](service-bus-geo-dr.md)
* [Azure SQL 数据库业务连续性][Azure SQL Database Business Continuity]
* [设计适用于 Azure 的弹性应用程序][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
