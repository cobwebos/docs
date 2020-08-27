---
title: 跨多个实例均衡分区负载 - Azure 事件中心 | Microsoft Docs
description: 介绍如何使用事件处理器和 Azure 事件中心 SDK 跨应用程序的多个实例均衡分区负载。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 8bf3f05b823a784f4f3fc2074719ed346f769f5e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933787"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>跨应用程序的多个实例均衡分区负载
若要缩放事件处理应用程序，可以运行应用程序的多个实例，并让这些实例自行进行负载均衡。 在旧版本中，[EventProcessorHost](event-hubs-event-processor-host.md) 允许在接收检查点事件时，在程序的多个实例与这些事件之间进行负载均衡。 在新版本（5.0 或以上）中，**EventProcessorClient**（.NET 和 Java）或 **EventHubConsumerClient**（Python 和 JavaScript）允许执行相同的操作。 使用事件使开发模型变得更简单。 通过注册事件处理程序来订阅你感兴趣的事件。

本文介绍使用多个实例从事件中心读取事件的示例方案，然后提供有关事件处理器客户端功能的详细信息。使用该客户端可以一次性从多个分区接收事件，并通过其他使用同一事件中心和使用者组的使用者进行负载均衡。

> [!NOTE]
> 缩放事件中心的关键在于分区使用者的思路。 与[竞争性使用者](/previous-versions/msp-n-p/dn568101(v=pandp.10))模式相比，分区使用者模式能够通过消除争用瓶颈和简化端到端的并行度，来实现较高的缩放度。

## <a name="example-scenario"></a>示例方案

作为示例方案，假设有一个家庭保安公司需要监控 100,000 个家庭。 该公司每隔一分钟就要从每个家庭中安装的各种传感器（例如运动检测器、门/窗打开传感器、玻璃破碎检测器等）获取数据。 该公司为居民提供一个网站，让他们近乎实时地监控家庭中的活动。

每个传感器将数据推送到事件中心。 在事件中心配置了 16 个分区。 在使用端，需要通过某个机制读取这些事件、合并事件（筛选器、聚合等）、将聚合数据转储到存储 Blob，然后投影到用户友好的网页上。

## <a name="write-the-consumer-application"></a>编写使用者应用程序

在分布式环境中设计时使用者时，方案必须处理以下要求：

1. **缩放：** 创建多个使用者，每个使用者获取若干事件中心分区的读取所有权。
2. **负载均衡：** 动态增加或减少使用者。 例如，将新的传感器类型（例如一氧化碳检测器）添加到每个家庭后，事件数会增多。 在这种情况下，操作员（人类）会增加使用者实例的数目。 然后，使用者池可以重新均衡它们拥有的分区数，以便与新添加的使用者分担负载。
3. **故障时无缝恢复：** 如果某个使用者（**使用者 A**）发生故障（例如，托管使用者的虚拟机突然崩溃），其他使用者能够拾取**使用者 A** 拥有的分区并继续。 此外，称作“检查点”或“偏移量”的延续点应该位于**使用者 A** 发生故障时的确切位置，或者略微在该位置的前面。 
4. **使用事件：** 尽管前面三个要点能够应对使用者的管理，但还必须提供代码来使用事件并对其执行有用的操作。 例如，聚合事件并将其上传到 Blob 存储。

## <a name="event-processor-or-consumer-client"></a>事件处理器或使用者客户端

无需生成自己的解决方案即可满足这些要求。 Azure 事件中心 SDK 提供此功能。 在 .NET 或 Java SDK 中使用事件处理器客户端 (EventProcessorClient)，在 Python 和 JavaScript SDK 中使用 EventHubConsumerClient。 在旧版 SDK 中，它是支持这些功能的事件处理器主机 (EventProcessorHost)。

对于大多数生产方案，我们建议使用事件处理器客户端来读取和处理事件。 处理器客户端旨在提供可靠的体验用于以高效且容错的方式跨事件中心的所有分区处理事件，同时提供设置处理进度检查点的方式。 在给定事件中心的使用者组上下文中，事件处理器客户端还能够以协作方式工作。 当实例可用或不可用于组时，客户端会自动管理工作的分配和均衡。

## <a name="partition-ownership-tracking"></a>分区所有权跟踪

事件处理器实例通常拥有并处理来自一个或多个分区的事件。 分区所有权在与事件中心和使用者组的组合相关联的所有活动事件处理器实例之间均匀分配。 

每个事件处理器具有唯一的标识符，通过在检查点存储中添加或更新条目来声明分区的所有权。 所有事件处理器实例定期与此存储通信，以更新自身的处理状态以及了解其他活动实例。 然后，使用此数据在活动处理器之间均衡负载。 新实例可以加入处理池以进行纵向扩展。 当实例发生故障时（无论是由于故障还是纵向缩减），分区所有权都会正常转移到其他活动处理器。

检查点存储中的分区所有权记录会跟踪事件中心命名空间、事件中心名称、使用者组、事件处理器标识符（也称为所有者）、分区 ID 和上次修改时间。



| 事件中心命名空间               | 事件中心名称 | **使用者组** | 所有者                                | 分区 ID | 上次修改时间  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | 解码的字符：                  |                                      |              |                     |
|                                    |                | 解码的字符：                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

每个事件处理器实例获取分区的所有权，并从上一个已知的[检查点](# Checkpointing)开始处理分区。 如果某个处理器出现故障（VM 关闭），其他实例将通过查看上次修改时间来检测此情况。 其他实例尝试获取之前由非活动实例所拥有的分区所有权，检查点存储保证只有一个实例能够成功声明分区所有权。 因此，在任意给定时间点，最多只有一个处理器从分区接收事件。

## <a name="receive-messages"></a>接收消息

创建事件处理器时，需要指定用于处理事件和错误的函数。 每次调用处理事件的函数都会从特定的分区传送单个事件。 你需要负责处理此事件。 若要确保使用者将每条消息至少处理一次，需要编写自己的包含重试逻辑的代码。 但请注意有害消息。

我们建议以相对较快的速度执行操作。 也就是说，尽量减少处理量。 如果需要写入存储并执行某种路由，最好是使用两个使用者组和两个事件处理器。

## <a name="checkpointing"></a>检查点

检查点是事件处理器标记或提交上次在分区中成功处理的事件位置的过程。 标记检查点通常在处理事件的函数内部执行，并在使用者组中按分区进行。 

如果事件处理器从分区断开连接，另一个实例可以在检查点位置继续处理该分区，该检查点是以前由该使用者组中该分区的最后一个处理器提交的。 当处理器建立连接时，它会将此偏移量传递给事件中心，以指定要从其开始读取数据的位置。 这样，你便可以使用检查点将事件标记为已由下游应用程序“完成”，并在事件处理器出现故障时提供复原能力。 若要返回到较旧的数据，可以在此检查点过程中指定较低的偏移量。 

执行检查点将事件标记为已处理时，将会根据事件偏移量和序列号在检查点存储中添加或更新某个条目。 用户应确定检查点的更新频率。 每次成功处理事件之后进行更新可能会影响性能和成本，因为这会对底层检查点存储触发写入操作。 另外，为每个事件设置检查点意味着对排队消息传递模式使用服务总线队列可能比使用事件中心更好。 事件中心背后的理念是大规模实现“至少一次”传递。 将下游系统指定为幂等，即可方便地在发生故障或重启（导致多次收到相同的事件）后恢复。

> [!NOTE]
> 如果你在一个环境中使用 Azure Blob 存储作为检查点存储，该环境支持与 Azure 上通常可用的存储 Blob SDK 版本不同的版本，那么你需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果在 [Azure Stack Hub 版本 2002 上运行事件中心](/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本为 2017-11-09。 在这种情况下，需要使用代码将存储服务 API 版本设定为 2017-11-09。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅“GitHub 上的这些示例”： 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs) 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) 或 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>线程安全性和处理程序实例

默认情况下，将会针对给定分区按顺序调用处理事件的函数。 当事件泵继续在后台的其他线程上运行时，来自同一分区的后续事件和对该函数的调用将在后台排队。 请注意，可以同时处理来自不同分区的事件，但必须同步跨分区访问的任何共享状态。

## <a name="next-steps"></a>后续步骤
参阅以下快速入门：

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
