---
title: "Azure 事件中心功能概述 | Microsoft Docs"
description: "有关 Azure 事件中心功能的概述和详细信息"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: a74d767d57eb5ce2b3a716f9ba908a451f25f538
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---

# <a name="event-hubs-features-overview"></a>事件中心功能概述

Azure 事件中心是可缩放的事件处理服务，它引入并处理大量事件和数据，具有低延迟和高可靠性。 有关服务的高级概述，请参阅[什么是事件中心？](event-hubs-what-is-event-hubs.md)。

本文基于[概述](event-hubs-what-is-event-hubs.md)中的信息编写，并提供有关事件中心组件和功能的技术和实现详细信息。

## <a name="event-publishers"></a>事件发布者

向事件中心发送数据的任何实体都是事件生成者或*事件发布者*。 事件发布者可以使用 HTTPS 或 AMQP 1.0 发布事件。 事件发布者通过共享访问签名 (SAS) 令牌向事件中心表明其身份，可以使用唯一的标识，也可以使用通用的 SAS 令牌。

### <a name="publishing-an-event"></a>发布事件

可以通过 AMQP 1.0 或 HTTPS 发布事件。 服务总线提供了[客户端库和类](event-hubs-dotnet-framework-api-overview.md)，这些库和类用于从 .NET 客户端将事件发布到事件中心。 对于其他运行时和平台，可以使用任何 AMQP 1.0 客户端，例如 [Apache Qpid](http://qpid.apache.org/)。 可以逐个或者批量发送事件。 单个发布（事件数据实例）限制为 256 KB，不管它是单个事件还是事件批。 发布大于此限制的事件将导致出错。 发布者最好是不知道事件中心内的分区数，而只是通过其 SAS 令牌指定“分区键”（如下一部分所述）或其标识。

是要使用 AMQP 还 HTTPS 根据具体的使用方案而定。 AMQP 除了需要使用传输级别安全 (TLS) 或 SSL/TLS 以外，还需要建立持久的双向套接字。 AMQP 在初始化会话时的网络成本更高，而 HTTPS 则每次请求都需要额外的 SSL 开销。 对于需要频繁进行发布的发布者来说，AMQP 的性能更高。

![事件中心](./media/event-hubs-features/partition_keys.png)

事件中心可确保按顺序将共享分区键值的所有事件传送到同一分区。 如果将分区键与发布者策略结合使用，则发布者的标识与分区键的值必须匹配。 否则会出错。

### <a name="publisher-policy"></a>发布者策略

事件中心可让你通过发布者策略对事件发布者进行精细控制。 发布者策略是运行时功能，旨在为大量独立的事件发布者提供方便。 借助发布者策略，每个发布者在使用以下机制将事件发布到事件中心时可以使用自身的唯一标识符。

```
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

不需要提前创建发布者名称，但它们必须与发布事件时使用的 SAS 令牌匹配，以确保发布者标识保持独立。 使用发布者策略时，**PartitionKey** 值将设置为发布者名称。 若要正常工作，这些值必须匹配。

## <a name="capture"></a>捕获

使用[事件中心捕获](event-hubs-capture-overview.md)，可以自动捕获事件中心的流式处理数据，并将其保存到所选 Blob 存储帐户或 Azure Data Lake 服务帐户。 可以从 Azure 门户启用捕获，并指定大小上限和时间范围以执行捕获。 使用事件中心捕获，用户可以指定自己的 Azure Blob 存储帐户和容器或 Azure Data Lake 服务帐户（用于存储已捕获数据）。 捕获的数据用 Apache Avro 格式编写。

## <a name="partitions"></a>分区

事件中心通过分区使用者模式提供消息流式处理功能，在此模式下，每个使用者只读取消息流的特定子集或分区。 此模式支持事件处理的水平缩放，同时提供队列和主题中不可用的其他面向流的功能。

分区是事件中心内保留的有序事件。 当较新的事件到达时，它们将添加到此序列的末尾。 可以将分区视为“提交日志”。

![事件中心](./media/event-hubs-features/partition.png)

事件中心按配置的保留时间保留数据，该时间适用于事件中心的所有分区。 事件根据特定的时间过期；无法显式删除事件。 由于分区互相独立且包含自身的数据序列，因此通常按不同速率增大。

![事件中心](./media/event-hubs-features/multiple_partitions.png)

分区数在创建时指定，必须介于 2 到 32 之间。 分区计数不可更改，因此在设置分区计数时应考虑长期规模。 分区是一种数据组织机制，与使用方应用程序中所需的下游并行度相关。 事件中心的分区数与预期会有的并发读取者数直接相关。 要将分区数增加到 32 以上，可以联系事件中心团队。

虽然可以标识分区并向其直接发送数据，但并不建议直接发送到分区， 而应使用[事件发布者](#event-publishers)和[容量](#capacity)部分介绍的更高级构造。 

分区中填充了一系列的事件数据，这些数据包含事件的正文、用户定义的属性包和元数据，例如，它在分区中的偏移量，以及它在流序列中的编号。

如需详细了解分区以及如何在可用性和可靠性之间进行取舍，请参阅[事件中心编程指南](event-hubs-programming-guide.md#partition-key)和[事件中心中的可用性和一致性](event-hubs-availability-and-consistency.md)这两篇文章。

### <a name="partition-key"></a>分区键

可以使用[分区键](event-hubs-programming-guide.md#partition-key)将传入事件数据映射到特定分区，以便进行数据组织。 分区键是发送者提供的、要传递给事件中心的值。 该键通过静态哈希函数进行处理，以便分配分区。 如果在发布事件时未指定分区键，则会使用循环分配。

事件发布者只知道其分区密钥，而不知道事件要发布到的分区。 键与分区的这种分离使发送者无需了解有关下游处理的过多信息。 每个设备或用户的唯一标识就可以充当一个适当的分区键，但是，也可以使用其他属性（例如地理位置），以便将相关的事件分组到单个分区中。

## <a name="sas-tokens"></a>SAS 令牌

事件中心使用在命名空间和事件中心级别提供的*共享访问签名*。 SAS 令牌是从 SAS 密钥生成的，它是以特定格式编码的 URL 的 SHA 哈希。 事件中心可以使用密钥（策略）的名称和令牌重新生成哈希，以便对发送者进行身份验证。 通常，为事件发布者创建的 SAS 令牌只对特定的事件中心具有**发送**权限。 此 SAS 令牌 URL 机制是“发布者策略”中介绍的发布者标识的基础。 有关使用 SAS 的详细信息，请参阅[使用服务总线进行共享访问签名身份验证](../service-bus-messaging/service-bus-sas.md)。

## <a name="event-consumers"></a>事件使用者

从事件中心读取事件数据的任何实体称为“事件使用者”。 所有事件中心使用者通过 AMQP 1.0 会话进行连接，事件在可用时通过会话传送。 客户端不需要轮询数据可用性。

### <a name="consumer-groups"></a>使用者组

事件中心的发布/订阅机制通过“使用者组”启用。 使用者组是整个事件中心的视图（状态、位置或偏移量）。 使用者组使多个消费应用程序都有各自独立的事件流视图，并按自身步调和偏移量独立读取流。

在流处理体系结构中，每个下游应用程序相当于一个使用者组。 如果要将事件数据写入长期存储，则该存储写入器应用程序就是一个使用者组。 然后即可由另一独立的使用者组执行复杂的事件处理。 只能通过使用者组访问分区。 每个使用者组的分区上最多可以有 5 个并发读取者，但是**建议每个使用者组的分区上只有一个活动接收者**。 事件中心内始终有一个默认的使用者组，最多可为一个标准层事件中心创建 20 个使用者组。

以下是使用者组 URI 约定的示例：

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

下图显示了事件中心流处理体系结构：

![事件中心](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>流偏移量

“偏移量”是事件在分区中的位置。 可以将偏移量视为客户端游标。 偏移量是事件的字节编号。 有了该偏移量，事件使用者（读取者）便可以在事件流中指定要从其开始读取事件的点。 可以时间戳或者偏移量值的形式指定偏移量。 使用者负责在事件中心服务的外部存储其自身的偏移量值。 在分区中，每个事件都包含一个偏移量。

![事件中心](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>检查点

*检查点* 是读取者在分区事件序列中标记或提交其位置时执行的过程。 检查点操作由使用者负责，并在使用者组中的每个分区上进行。 这种责任意味着，对于每个使用者组而言，每个分区读取者必须跟踪它在事件流中的当前位置，当它认为数据流已完成时，可以通知服务。

如果读取者与分区断开连接，当它重新连接时，将开始读取前面由该使用者组中该分区的最后一个读取者提交的检查点。 当读取者建立连接时，它会将此偏移量传递给事件中心，以指定要从其开始读取数据的位置。 这样，用户便可以使用检查点将事件标记为已由下游应用程序“完成”，并且在不同计算机上运行的读取者之间发生故障转移时，还可以提供弹性。 若要返回到较旧的数据，可以在此检查点过程中指定较低的偏移量。 借助此机制，检查点可以实现故障转移弹性和事件流回放。

### <a name="common-consumer-tasks"></a>常见的使用者任务

所有事件中心使用者都通过 AMQP 1.0 会话，一种状态感知型双向信道进行连接。 每个分区都提供一个 AMQP 1.0 会话，方便传输按分区隔离的事件。

#### <a name="connect-to-a-partition"></a>连接到分区

在连接到分区时，常见的做法是使用租用机制来协调读取者与特定分区的连接。 这样，便可以做到一个使用者组中每分区只有一个活动的读取者。 使用 .NET 客户端的 [EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost) 类可以简化检查点、租用和读取者管理功能。 事件处理程序主机是智能使用者代理。

#### <a name="read-events"></a>读取事件

为特定分区建立 AMQP 1.0 会话和链接后，事件中心服务会将事件传送到 AMQP 1.0 客户端。 与 HTTP GET 等基于提取的机制相比，此传送机制可以实现更高的吞吐量和更低的延迟。 将事件发送到客户端时，每个事件数据实例将包含重要的元数据，例如，用于简化对事件序列执行的检查点操作的偏移量和序列号。

事件数据：
* Offset
* 序列号
* 正文
* 用户属性
* 系统属性

管理偏移量由用户负责。

## <a name="capacity"></a>容量

事件中心具有高度可伸缩的并行体系结构，在进行大小和规模调整时需要考虑多个重要因素。

### <a name="throughput-units"></a>吞吐量单位

事件中心的吞吐量容量由“吞吐量单位”控制。 吞吐量单位是预先购买的容量单位。 单个吞吐量单位包括以下容量：

* 入口：最高每秒 1 MB 或每秒 1000 个事件（以先达到的限制为准）
* 出口：最高每秒 2 MB

超出所购吞吐量单位的容量时，入口受限，返回 [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 出口不会生成限制异常，但仍受限于所购吞吐量单位的容量。 如果收到发布速率异常或者预期会看到更高的出口，请务必检查为命名空间购买了多少吞吐量单位。 可以在 [Azure 门户](https://portal.azure.com)的命名空间的“规模”边栏选项卡上管理吞吐量单位。 也可使用[事件中心 API](event-hubs-api-overview.md) 以编程方式管理吞吐量单位。

吞吐量单位按小时计费，需提前购买。 购买后，吞吐量单位的最短计费时限为一小时。 最多可以为一个事件中心命名空间购买 20 个吞吐量单位，这些单位在命名空间的所有事件中心共享。

联系 Azure 支持部门即可购买更多吞吐量单位（以块的形式购买，每个块 20 个单位，最多可购买 100 个单位）。 除此之外，也可以购买包含 100 个吞吐量单位的块。

建议权衡吞吐量单位和分区数目，实现最佳缩放。 一个分区最多只能缩放一个吞吐量单位。 吞吐量单位数应小于或等于事件中心内的分区数。

如需事件中心的详细定价信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="next-steps"></a>后续步骤

有关事件中心的详细信息，请访问以下链接：

* 使用[事件中心教程][Event Hubs tutorial]入门
* [事件中心编程指南](event-hubs-programming-guide.md)
* [事件中心中的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中心常见问题](event-hubs-faq.md)
* [事件中心示例][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[事件中心示例]: https://github.com/Azure/azure-event-hubs/tree/master/samples

