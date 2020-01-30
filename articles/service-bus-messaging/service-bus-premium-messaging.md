---
title: Azure 服务总线高级层和标准层
description: 本文介绍 Azure 服务总线的 "标准" 层和 "高级" 层。 比较这些层并提供技术差异。
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774570"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服务总线高级和标准消息传送层

服务总线消息传送（包括队列和主题等实体）将企业消息传送功能与丰富的云规模发布-订阅语义结合在一起。 对于许多完善的云解决方案，服务总线消息传送会用作通信基础结构。

服务总线消息传送的*高级*层将解决有关任务关键应用程序的规模、性能和可用性的常见客户请求。 高级层建议用于生产方案。 虽然功能集几乎完全相同，但这两个层的服务总线消息传送旨在用于满足不同的使用情形。

下表突出显示了部分高层差异。

| 高级 | 标准 |
| --- | --- |
| 高吞吐量 |可变吞吐量 |
| 可预测的性能 |可变滞后时间 |
| 固定价格 |即用即付可变定价 |
| 可以扩展和缩减工作负荷 |N/A |
| 消息大小最大为 1 MB |消息大小最大为 256 KB |

**服务总线高级消息传送**在 CPU 和内存级别提供资源隔离，以便每个客户工作负荷以隔离方式运行。 此资源容器称为 *消息传送单元*。 每个高级命名空间至少会分配一个消息传送单元。 可以为每个服务总线高级命名空间购买1、2、4或8个消息传送单元。 单个工作负荷或实体可以跨多个消息传送单元，而消息传送单元数可以随时更改。 从而为基于服务总线的解决方案提供可预测和稳定的性能。

此性能不仅更易于预测和实现，而且速度更快。 服务总线高级消息传送以在 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)引入的存储引擎为基础。 使用高级消息传送，峰值性能比使用标准层快得多。

## <a name="premium-messaging-technical-differences"></a>高级消息传送技术差异

以下部分介绍高级和标准消息传送层之间的一些差异。

### <a name="partitioned-queues-and-topics"></a>分区的队列和主题

高级消息传送不支持分区队列和主题。 有关分区的详细信息，请参阅 [分区的队列和主题](service-bus-partitioning.md)。

### <a name="express-entities"></a>快速实体

由于高级消息传送在一个完全隔离的运行时环境中运行，因此高级命名空间中不支持快速实体。 有关快速功能的详细信息，请参阅 [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 属性。

如果有在标准传送下运行的代码并且希望将其移植到高级层，请确保将 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 属性设置为 **false**（默认值）。

## <a name="premium-messaging-resource-usage"></a>高级消息传送资源使用情况
通常，对实体进行的任何操作都可能导致 CPU 和内存使用率。 下面是其中一些操作： 

- 对队列、主题和订阅的管理操作，如 CRUD （创建、检索、更新和删除）操作。
- 运行时操作（发送和接收消息）
- 监视操作和警报

但额外的 CPU 和内存使用情况并不是定价的。 对于高级消息传送层，消息单位有单个价格。

由于以下原因，将跟踪并显示 CPU 和内存使用情况： 

- 提供系统内部的透明度
- 了解购买的资源的容量。
- 可帮助您决定增加/减少的容量规划。

## <a name="messaging-unit---how-many-are-needed"></a>消息单元-需要多少？

预配 Azure 服务总线高级命名空间时，必须指定分配的消息传送单元数。 这些消息传送单元是分配给命名空间的专用资源。

可以**动态调整**分配给服务总线高级命名空间的消息传送单元数，以考虑工作负荷中的更改（增加或减少）。

为体系结构确定消息单元数量时，需要考虑以下几个因素：

- 从分配给命名空间的***1 或2个消息传送单元***开始。
- 研究命名空间的[资源使用情况](service-bus-metrics-azure-monitor.md#resource-usage-metrics)指标内的 CPU 使用情况指标。
    - 如果 CPU 使用率***低于 20%***，则可以***缩减***分配给命名空间的消息传送单元数。
    - 如果 CPU 使用率***高于 70%***，则你的应用程序将从***扩展***分配给命名空间的消息传送单元数中获益。

可以使用[Azure 自动化 runbook](../automation/automation-quickstart-create-runbook.md)自动执行将分配给服务总线命名空间的资源进行扩展的过程。

> [!NOTE]
> 分配给命名空间的资源的**缩放**可以是抢先或被动。
>
>  * **抢先**：如果需要其他工作负荷（由于季节性或趋势），可以在工作负荷命中之前继续向命名空间分配更多消息传送单元。
>
>  * **反应**：如果通过研究资源使用情况指标来识别其他工作负荷，则可以将其他资源分配给命名空间以合并增加的需求。
>
> 服务总线的计费指标为每小时。 对于纵向扩展，只需为使用这些资源的小时数付费。
>

## <a name="get-started-with-premium-messaging"></a>高级消息传送入门

高级消息传送很容易入门，其操作过程类似于标准消息传送。 一开始时，请在 [Azure 门户](https://portal.azure.com)中[创建命名空间](service-bus-create-namespace-portal.md)。 确保在“定价层”下选择“高级”。 单击“查看完整的定价详细信息”以查看有关每个层级的详细信息。

![create-premium-namespace][create-premium-namespace]

也可以[使用 Azure 资源管理器模板创建高级命名空间](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/)。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下链接：

* [Azure 服务总线高级消息传送简介（博客文章）](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服务总线高级消息传送简介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
