---
title: Azure 事件中心功能概述 | Microsoft Docs
description: 本文详细介绍 Azure 事件中心的功能和术语。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c16dd4345e62fa9e826e657cce9a752186ec1b82
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628651"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure 事件中心的功能和术语

Azure 事件中心是可缩放的事件处理服务，它引入并处理大量事件和数据，具有低延迟和高可靠性。 有关简要概述，请参阅[什么是事件中心？](event-hubs-what-is-event-hubs.md)。

本文基于[概述文章](event-hubs-what-is-event-hubs.md)中的信息编写，并提供有关事件中心组件和功能的技术和实现详细信息。

## <a name="namespace"></a>命名空间
事件中心命名空间提供唯一的范围容器，可以通过其[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)进行引用，而在该容器中，可以创建一个或多个事件中心或 Kafka 主题。 

## <a name="event-hubs-for-apache-kafka"></a>用于 Apache Kafka 的事件中心

[此功能](event-hubs-for-kafka-ecosystem-overview.md)提供了一个终结点，该终结点允许客户使用 Kafka 协议与事件中心进行通信。 此集成为客户提供了一个 Kafka 终结点。 这允许客户将其现有 Kafka 应用程序配置为与事件中心进行通信，提供了运行其自己的 Kafka 群集的替代方式。 Apache Kafka 的事件中心支持 Kafka 协议 1.0 和更高版本。 

通过这种集成，无需运行 Kafka 群集或通过 Zookeeper 管理这些群集。 这还允许你使用事件中心的一些要求最严苛的功能，例如“捕获”、“自动扩展”和“异地灾难恢复”。

使用此集成，只需要进行配置更改，便可允许应用程序（例如 Mirror Maker）或框架（例如 Kafka Connect）以非群集方式工作。 

## <a name="event-publishers"></a>事件发布者

向事件中心发送数据的任何实体都是事件生成者或*事件发布者*。 事件发布者可以使用 HTTPS、AMQP 1.0 或 Kafka 1.0 和更高版本发布事件。 事件发布者通过共享访问签名 (SAS) 令牌向事件中心表明其身份，可以使用唯一的标识，也可以使用通用的 SAS 令牌。

### <a name="publishing-an-event"></a>发布事件

可以通过 AMQP 1.0、Kafka 1.0（和更高版本）或 HTTPS 发布事件。 服务总线提供了[客户端库和类](event-hubs-dotnet-framework-api-overview.md)，这些库和类用于从 .NET 客户端将事件发布到事件中心。 对于其他运行时和平台，可以使用任何 AMQP 1.0 客户端，例如 [Apache Qpid](https://qpid.apache.org/)。 可以逐个或者批量发送事件。 单个发布（事件数据实例）限制为 1 MB，不管它是单个事件还是事件批。 发布大于此限制的事件将导致出错。 发布者最好是不知道事件中心内的分区，而只是通过其 SAS 令牌指定*分区键*（在下一节中介绍）或其标识。

是要使用 AMQP 还 HTTPS 根据具体的使用方案而定。 AMQP 除了需要使用传输级别安全 (TLS) 或 SSL/TLS 以外，还需要建立持久的双向套接字。 初始化会话时，AMQP 的网络成本更高，但对于每个请求，HTTPS 都需要额外的 TLS 开销。 对于需要频繁进行发布的发布者来说，AMQP 的性能更高。

![事件中心](./media/event-hubs-features/partition_keys.png)

事件中心可确保按顺序将共享分区键值的所有事件传送到同一分区。 如果将分区键与发布者策略结合使用，则发布者的标识与分区键的值必须匹配。 否则将会出错。

### <a name="publisher-policy"></a>发布者策略

事件中心可让你通过 *发布者策略*对事件发布者进行精细控制。 发布者策略是运行时功能，旨在为大量独立的事件发布者提供方便。 借助发布者策略，每个发布者在使用以下机制将事件发布到事件中心时可以使用自身的唯一标识符。

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

不需要提前创建发布者名称，但它们必须与发布事件时使用的 SAS 令牌匹配，以确保发布者标识保持独立。 使用发布者策略时，**PartitionKey** 值将设置为发布者名称。 若要正常工作，这些值必须匹配。

## <a name="capture"></a>捕获

使用[事件中心捕获](event-hubs-capture-overview.md)，可以自动捕获事件中心的流式处理数据，并将其保存到所选 Blob 存储帐户或 Azure Data Lake 服务帐户。 可以从 Azure 门户启用捕获，并指定大小上限和时间范围以执行捕获。 使用事件中心捕获，用户可以指定自己的 Azure Blob 存储帐户和容器或 Azure Data Lake 服务帐户（其中之一用于存储已捕获数据）。 捕获的数据用 Apache Avro 格式编写。

## <a name="partitions"></a>分区
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS 令牌

事件中心使用*共享访问签名*，可在命名空间和事件中心级别使用。 SAS 令牌是从 SAS 密钥生成的，它是以特定格式编码的 URL 的 SHA 哈希。 事件中心可以使用密钥（策略）的名称和令牌重新生成哈希，以便对发送者进行身份验证。 通常，为事件发布者创建的 SAS 令牌只对特定的事件中心具有 "**发送**" 权限。 此 SAS 令牌 URL 机制是“发布者策略”中介绍的发布者标识的基础。 有关使用 SAS 的详细信息，请参阅[使用服务总线进行共享访问签名身份验证](../service-bus-messaging/service-bus-sas.md)。

## <a name="event-consumers"></a>事件使用者

从事件中心读取事件数据的任何实体都是*事件使用者*。 所有事件中心使用者通过 AMQP 1.0 会话进行连接，事件在可用时通过会话传送。 客户端不需要轮询数据可用性。

### <a name="consumer-groups"></a>使用者组

事件中心的发布/订阅机制是通过*使用者组*实现的。 使用者组是整个事件中心的视图（状态、位置或偏移量）。 使用者组使多个消费应用程序都有各自独立的事件流视图，并按自身步调和偏移量独立读取流。

在流处理体系结构中，每个下游应用程序相当于一个使用者组。 如果要将事件数据写入长期存储，则该存储写入器应用程序就是一个使用者组。 然后即可由另一独立的使用者组执行复杂的事件处理。 只能通过使用者组访问分区。 事件中心内始终有一个默认的使用者组，最多可为一个标准层事件中心创建 20 个使用者组。

每个使用者组的分区上最多可以有 5 个并发读取者，但是**建议每个使用者组的分区上只有一个活动接收者**。 在单个分区中，每个读取者接收所有消息。 如果在同一分区上有多个读取者，则处理重复消息。 需在代码中处理此问题，这并非易于处理的。 但是，在某些情况下，这是一种有效的方法。


以下是使用者组 URI 约定的示例：

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

下图显示了事件中心流处理体系结构：

![事件中心](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>流偏移量

“偏移量”是事件在分区中的位置。** 可以将偏移量视为客户端游标。 偏移量是事件的字节编号。 有了该偏移量，事件使用者（读取者）便可以在事件流中指定要从其开始读取事件的点。 可以时间戳或者偏移量值的形式指定偏移量。 使用者负责在事件中心服务的外部存储其自身的偏移量值。 在分区中，每个事件都包含一个偏移量。

![事件中心](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>检查点

*检查点* 是读取者在分区事件序列中标记或提交其位置时执行的过程。 检查点操作由使用者负责，并在使用者组中的每个分区上进行。 这种责任意味着，对于每个使用者组而言，每个分区读取者必须跟踪它在事件流中的当前位置，当它认为数据流已完成时，可以通知服务。

如果读取者与分区断开连接，当它重新连接时，将开始读取前面由该使用者组中该分区的最后一个读取者提交的检查点。 当读取者建立连接时，它会将此偏移量传递给事件中心，以指定要从其开始读取数据的位置。 这样，用户便可以使用检查点将事件标记为已由下游应用程序“完成”，并且在不同计算机上运行的读取者之间发生故障转移时，还可以提供弹性。 若要返回到较旧的数据，可以在此检查点过程中指定较低的偏移量。 借助此机制，检查点可以实现故障转移弹性和事件流回放。

> [!NOTE]
> 如果使用 Azure Blob 存储作为支持不同版本的存储 Blob SDK 的环境中的检查点存储，则需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果在[Azure Stack 集线器版本2002上运行事件中心](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本为2017-11-09 版。 在这种情况下，需要使用代码将存储服务 API 版本设定为2017-11-09。 有关如何以特定存储 API 版本为目标的示例，请参阅 GitHub 上的以下示例： 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)。 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript)或[TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>常见的使用者任务

所有事件中心使用者都通过 AMQP 1.0 会话，一种状态感知型双向信道进行连接。 每个分区都提供一个 AMQP 1.0 会话，方便传输按分区隔离的事件。

#### <a name="connect-to-a-partition"></a>连接到分区

在连接到分区时，常见的做法是使用租用机制来协调读取者与特定分区的连接。 这样，便可以做到一个使用者组中每分区只有一个活动的读取者。 使用 .NET 客户端的 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 类可以简化检查点、租用和读取者管理功能。 事件处理程序主机是智能使用者代理。

#### <a name="read-events"></a>读取事件

为特定分区建立 AMQP 1.0 会话和链接后，事件中心服务会将事件传送到 AMQP 1.0 客户端。 与 HTTP GET 等基于提取的机制相比，此传送机制可以实现更高的吞吐量和更低的延迟。 将事件发送到客户端时，每个事件数据实例将包含重要的元数据，例如，用于简化对事件序列执行的检查点操作的偏移量和序列号。

事件数据：
* 偏移量
* 序列号
* Body
* 用户属性
* 系统属性

管理偏移量由用户负责。

## <a name="next-steps"></a>后续步骤

有关事件中心的详细信息，请访问以下链接：

- 事件中心入门
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [事件中心编程指南](event-hubs-programming-guide.md)
* [事件中心中的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中心常见问题](event-hubs-faq.md)
* [事件中心示例][]

[事件中心示例]: https://github.com/Azure/azure-event-hubs/tree/master/samples
