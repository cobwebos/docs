---
title: "Azure 服务总线高级和标准消息传送定价层概述 | Microsoft Docs"
description: "服务总线高级和标准消息传送层"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: darosa;sethm
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: a0c169e0580468e83a07c077f8c60e83d3fb52f2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服务总线高级和标准消息传送层

服务总线消息传送（包括队列和主题等实体）将企业消息传送功能与丰富的云规模发布-订阅语义结合在一起。 对于许多完善的云解决方案，服务总线消息传送会用作通信基础结构。

服务总线消息传送的*高级*层将解决有关任务关键应用程序的规模、性能和可用性的常见客户请求。 虽然功能集几乎完全相同，但这两个层的服务总线消息传送旨在用于满足不同的使用情形。

下表突出显示了部分高层差异。

| 高级 | 标准 |
| --- | --- |
| 高吞吐量 |可变吞吐量 |
| 可预测性能 |可变滞后时间 |
| 固定价格 |即用即付可变定价 |
| 可以扩展和缩减工作负荷 |不适用 |
| 消息大小最大为 1 MB |消息大小最大为 256 KB |

**服务总线高级消息传送**在 CPU 和内存级别提供资源隔离，以便每个客户工作负荷以隔离方式运行。 此资源容器称为 *消息传送单元*。 每个高级命名空间至少会分配一个消息传送单元。 可以为每个服务总线高级命名空间购买 1、2 或 4 个消息传送单元。 单一工作负荷或实体可以跨多个消息传送单元，尽管计费以 24 小时或每天的费率收取，但仍然可以随意更改消息传送单元数。 从而为基于服务总线的解决方案提供可预测和稳定的性能。

此性能不仅更易于预测和实现，而且速度更快。 服务总线高级消息传送以在 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)引入的存储引擎为基础。 使用高级消息传送，峰值性能比使用标准层快得多。

## <a name="premium-messaging-technical-differences"></a>高级消息传送技术差异

以下部分介绍高级和标准消息传送层之间的一些差异。

### <a name="partitioned-queues-and-topics"></a>分区的队列和主题

高级消息传送支持分区的队列和主题；实际上，这些实体始终分区（且不能禁用）。 但是，高级层中分区的队列和主题的工作方式不同于服务总线消息传送的标准层。 高级消息传送不使用 SQL 作为数据存储，并且也不再具有与共享平台相关联的可能的资源竞争。 因此，不需要为提高性能而分区。 此外，分区计数已从标准消息传送的 16 个减少到高级中的 2 个分区。 使用两个分区可确保可用性并且更适合高级运行时环境所要求的分区数。 

使用高级消息传送时，当将实体大小指定为 [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes) 时，该大小在 2 个分区之间平均拆分，这不同于[标准分区实体](service-bus-partitioning.md#standard)，在这种实体中，总大小是指定大小的 16 倍。 

有关分区的详细信息，请参阅 [分区的队列和主题](service-bus-partitioning.md)。

### <a name="express-entities"></a>快速实体

由于高级消息传送在一个完全隔离的运行时环境中运行，因此高级命名空间中不支持快速实体。 有关快速功能的详细信息，请参阅 [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 属性。

如果有在标准传送下运行的代码并且希望将其移植到高级层，请确保将 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 属性设置为 **false**（默认值）。

## <a name="get-started-with-premium-messaging"></a>高级消息传送入门

高级消息传送很容易入门，其操作过程类似于标准消息传送。 首先[创建一个命名空间](service-bus-create-namespace-portal.md)。 确保在“定价层”下选择“高级”。

![create-premium-namespace][create-premium-namespace]

也可以[使用 Azure Resource Manager 模板创建高级命名空间](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/)。


## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

* [Azure 服务总线高级消息传送简介（博客文章）](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服务总线高级消息传送简介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [如何使用 Service Bus 队列](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png

