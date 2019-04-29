---
title: 使用事件处理程序主机接收事件 - Azure 事件中心 | Microsoft Docs
description: 本文介绍 Azure 事件中心中的事件处理程序主机，它简化了检查点操作、租用和读取事件的管理。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 26f0abb48ba268f79167ed5d00e4f96d8b5e5998
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821873"
---
# <a name="receive-events-from-azure-event-hubs-using-event-processor-host"></a>使用事件处理程序主机从 Azure 事件中心接收事件

Azure 事件中心是强大的遥测引入服务，使用它能以较低的成本流式传输数百万个事件。 本文介绍如何通过*事件处理程序主机* (EPH) 使用引用的事件；EPH 是一个智能使用者代理，可以简化检查点、租用和并行事件读取器的管理。  

缩放事件中心的关键在于分区使用者的思路。 与[竞争性使用者](https://msdn.microsoft.com/library/dn568101.aspx)模式相比，分区使用者模式能够通过消除争用瓶颈和简化端到端的并行度，来实现较高的缩放度。

## <a name="home-security-scenario"></a>家庭保安方案

作为示例方案，假设有一个家庭保安公司需要监控 100,000 个家庭。 该公司每隔一分钟就要从每个家庭中安装的各种传感器（例如运动检测器、门/窗打开传感器、玻璃破碎检测器等）获取数据。 该公司为居民提供一个网站，让他们近乎实时地监控家庭中的活动。

每个传感器将数据推送到事件中心。 在事件中心配置了 16 个分区。 在使用端，需要通过某个机制读取这些事件、合并事件（筛选器、聚合等）、将聚合数据转储到存储 Blob，然后投影到用户友好的网页上。

## <a name="write-the-consumer-application"></a>编写使用者应用程序

在分布式环境中设计时使用者时，方案必须处理以下要求：

1. **缩放：** 创建多个使用者，每个使用者获取若干事件中心分区的读取所有权。
2. **负载均衡：** 动态增加或减少使用者。 例如，将新的传感器类型（例如一氧化碳检测器）添加到每个家庭后，事件数会增多。 在这种情况下，操作员（人类）会增加使用者实例的数目。 然后，使用者池可以重新均衡它们拥有的分区数，以便与新添加的使用者分担负载。
3. **故障时无缝恢复：** 如果某个使用者（使用者 A）发生故障（例如，托管使用者的虚拟机突然崩溃），其他使用者必须能够拾取使用者 A 拥有的分区并继续。 此外，称作“检查点”或“偏移量”的延续点应该位于**使用者 A** 发生故障时的确切位置，或者略微在该位置的前面。
4. **使用事件：** 尽管前面三个要点能够应对使用者的管理，但还必须提供代码来使用事件并对其执行有用的操作；例如，聚合事件并将其上传到 Blob 存储。

你无需为此生成自己的解决方案，事件中心会通过 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 接口和 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 类提供此功能。

## <a name="ieventprocessor-interface"></a>IEventProcessor 接口

首先，使用事件的应用程序会实现 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 接口，该接口有四种方法：[OpenAsync、CloseAsync、ProcessErrorAsync 和 ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods)。 此接口包含实际的代码用于使用事件中心发送的事件。 以下代码演示了一个简单的实现：

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

接下来，实例化 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 实例。 根据具体的重载，在构造函数中创建 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 实例时，将使用以下参数：

- **hostName：** 每个使用者实例的名称。 **EventProcessorHost** 的每个实例必须在使用者组中对此变量使用唯一值，因此，请不要对此值进行硬编码。
- **eventHubPath：** 事件中心的名称。
- **consumerGroupName：** 事件中心使用 $Default 作为默认使用者组的名称，但合理的做法是创建一个使用者组，以进行特定方面的处理。
- **eventHubConnectionString：** 事件中心的连接字符串，可从 Azure 门户中检索。 此连接字符串应该对事件中心拥有“侦听”权限。
- **storageConnectionString：** 用于内部资源管理的存储帐户。

最后，使用者将 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 实例注册到事件中心服务。 向 EventProcessorHost 实例注册事件处理程序类会启动事件处理。 注册操作告知事件中心服务预期使用者应用会使用其某些分区发送的事件，并且每当推送要使用的事件时，都要调用 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 实现代码。 


### <a name="example"></a>示例

例如，假设有 5 个虚拟机 (VM) 专门用于使用事件，每个 VM 中的简单控制台应用程序执行实际的使用工作。 每个控制台应用程序创建一个 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 实例，并将其注册到事件中心服务。

在此示例场景中，我们假设已将 16 个分区分配到了 5 个 **EventProcessorHost** 实例。 某些 **EventProcessorHost** 实例拥有的分区可能比其他实例要多。 对于 **EventProcessorHost** 实例拥有的每个分区，该应用程序会创建 `SimpleEventProcessor` 类的实例。 因此，总共有 16 个 `SimpleEventProcessor` 实例，并有一个实例分配到了每个分区。

以下列表对此示例做了总结：

- 16 个事件中心分区。
- 5 个 VM，每个 VM 中有 1 个使用者应用（例如 Consumer.exe）。
- Consumer.exe 注册的 5 个 EPH 实例，每个 VM 中各注册了 1 个。
- 5 个 EPH 实例创建的 16 个 `SimpleEventProcessor` 对象。
- 1 个 Consumer.exe 应用可能包含 4 个 `SimpleEventProcessor` 对象，因为 1 个 EPH 实例可以拥有 4 个分区。

## <a name="partition-ownership-tracking"></a>分区所有权跟踪

通过提供用于跟踪的 Azure 存储帐户来跟踪 EPH 实例（或使用者）的分区所有权。 可以通过一个简单的表将跟踪信息可视化，如下所示。 可以通过检查所提供的存储帐户下的 Blob 来查看实际的实现：

| **使用者组名称** | **分区 ID** | **主机名（所有者）** | **租约（或所有权）获取时间** | **分区（检查点）中的偏移量** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumer\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumer\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumer\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumer\_VM3 | 2018-04-15T01:22:56 | 976 |

此处，每个主机按特定的持续时间（租约持续时间）获取分区所有权。 如果某个主机发生故障（VM 关闭），则租约将会过期。 其他主机尝试获取分区所有权，其中一个主机会成功。 此过程会重置具有新所有者的分区上的租约。 这样，每次只会有一个读取者可以从使用者组中任意给定的分区读取事件。

## <a name="receive-messages"></a>接收消息

每次调用 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 都会提供事件的集合。 你需要负责处理这些事件。 如果要确保处理器主机将每条消息至少处理一次，则需要编写自己的继续重试代码。 但请注意有害消息。

建议以相对较快的速度执行操作；也就是说，尽量减少处理量。 改用使用者组。 如果需要写入存储并执行某种路由，最好是使用两个使用者组，并使用两个可以单独运行的 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 实现。

在处理过程中的某个阶段，你可能想要跟踪已读取和已完成哪些信息。 如果必须重新开始读取，以免返回到流的开头，则保持跟踪至关重要。 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 使用检查点简化了这种跟踪。 检查点是给定使用者组中给定分区的位置或偏移量，你希望在此位置处理消息。 在 **EventProcessorHost** 中标记检查点的过程是通过在 [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) 对象中调用 [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) 方法实现的。 此操作是在 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 方法中完成，但也可以在 [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) 中完成。

## <a name="checkpointing"></a>检查点

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) 方法有两个重载：首先，是在不使用任何参数的情况下，将检查点设置为 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 返回的集合中最高的事件偏移量。 此偏移量是一个“高水印”；它假设在调用此方法时已处理所有最近事件。 如果按这种方式使用此方法，请注意，应在返回其他事件处理代码之后再调用此方法。 第二个重载用于指定要设置检查点的 [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) 实例。 通过此方法可以使用不同类型的水印来设置检查点。 使用此水印可以实现一个“低水印”：已处理你确认的最低序列化事件。 提供此重载的目的是实现偏移量管理的灵活性。

执行检查点之后，会将一个包含分区特定信息（具体而言，是偏移量）的 JSON 文件写入到通过构造函数提供给 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 的存储帐户。 此文件不断更新。 请务必在上下文中考虑检查点 - 为每条消息设置检查点并不明智。 用于设置检查点的存储帐户可能不会处理此负载，但更重要的是，为每个事件设置检查点意味着对排队消息传递模式使用服务总线队列可能比使用事件中心更好。 事件中心背后的理念是大规模实现“至少一次”传递。 将下游系统指定为幂等，即可方便地在发生故障或重启（导致多次收到相同的事件）后恢复。

## <a name="thread-safety-and-processor-instances"></a>线程安全性和处理程序实例

默认情况下，[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 是线程安全的，以相对于 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 实例的同步方式运行。 当事件抵达某个分区时，会在 **IEventProcessor** 实例上针对该分区调用 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)，并阻止针对该分区进一步调用 **ProcessEventsAsync**。 后续消息和 **ProcessEventsAsync** 调用在幕后排队，因为消息泵持续在其他线程上后台运行。 此线程安全性消除了线程安全集合的需要，并显著提高了性能。

## <a name="shut-down-gracefully"></a>正常关闭

最后，[EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 能够干净关闭所有分区读取器，始终应该在关闭 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 的实例时调用它。 否则，在由于租约过期和时期冲突而启动 **EventProcessorHost** 的其他实例时可能导致延迟。 本文的 [Epoch](#epoch) 部分详细介绍了 Epoch 管理。 

## <a name="lease-management"></a>租约管理
向 EventProcessorHost 实例注册事件处理程序类会启动事件处理。 主机实例租用事件中心的一些分区，可能会从其他主机实例中获取一些租用，以实现跨所有主机实例均匀分布分区。 对于每个租用分区，主机实例先创建所提供事件处理程序类的实例，再从相应分区接收事件，并将它们传递给事件处理程序实例。 随着添加的实例和获取的租用变多，EventProcessorHost 最终会均衡所有使用者之间的负载。

如前所述，跟踪表大大简化了 [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 的自动缩放性。 当 **EventProcessorHost** 的实例启动时，会尽可能多地获取租约，并开始读取事件。 当租约即将过期时，**EventProcessorHost** 会尝试通过预留来续订租约。 如果租约可续订，则处理程序会继续读取，但如果租约不可续订，则会关闭读取器并调用 [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)。 很适合在调用 **CloseAsync** 时针对该分区执行任何最终清理。

**EventProcessorHost** 包含 [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) 属性。 使用此属性可以控制租约管理。 请在注册 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 实现之前设置这些选项。

## <a name="control-event-processor-host-options"></a>控制事件处理程序主机选项

此外，[RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) 的一个重载采用 [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) 对象作为参数。 使用此参数可以控制 [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 本身的行为。 [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) 定义四个属性和一个事件：

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize)：要在 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 调用中接收的集合的最大大小。 这不是最小大小，而只是最大大小。 如果要接收的消息较少，将使用可用数量的消息执行 **ProcessEventsAsync**。
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount)：底层 AMQP 通道在确定客户端应接收的消息数上限时使用的值。 此值应大于或等于 [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize)。
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout)：如果此参数为 **true**，则当接收分区中事件的底层调用超时时，会调用 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)。在分区不活动期间，可以使用此方法执行基于时间的操作。
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)：用于设置函数指针或 lambda 表达式，当读取器开始读取分区时，可以调用该函数或表达式来提供初始偏移量。 如果未指定此偏移量，读取器会从最旧的事件开始，除非已在提供给 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 构造函数的存储帐户中保存了包含偏移量的 JSON 文件。 若要更改读取器的启动行为，此方法很有用。 调用此方法时，对象参数将包含正在为其启动读取器的分区 ID。
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs)：用于接收 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 中发生的任何底层异常的通知。 如果操作未按预期进行，很适合从此事件开始调查。

## <a name="epoch"></a>Epoch

下面是接收 Epoch 的工作原理：

### <a name="with-epoch"></a>使用 Epoch
Epoch 是服务用来强制实施分区/租约所有权的唯一标识符（Epoch 值）。 使用 [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) 方法创建基于 Epoch 的接收器。 此方法创建基于 Epoch 的接收器。 该接收器是针对指定使用者组中的特定事件中心分区创建的。

Epoch 功能可让用户确保在任意时间点使用者组中只有一个接收器，并附带以下规则：

- 如果使用者组中没有任何现有的接收器，则用户可以使用任何 Epoch 值创建接收器。
- 如果某个接收器的 Epoch 值为 e1，创建的新接收器的 Epoch 值为 e2，而 e1 <= e2，那么，使用 e1 值的接收器将自动断开连接，使用 e2 值的接收器将成功创建。
- 如果某个接收器的 Epoch 值为 e1，创建的新接收器的 Epoch 值为 e2，而 e1 > e2，那么，创建 e2 将会失败并出现错误：使用 Epoch e1 的接收器已存在。

### <a name="no-epoch"></a>无 Epoch
使用 [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) 方法创建不是基于 Epoch 的接收器。 

在流处理中，用户有时想要在单个使用者组中创建多个接收器。 若要支持此类方案，我们确实可以创建一个不带 Epoch 的接收器；在本例中，我们最多允许在使用者组中创建 5 个并发的接收器。

### <a name="mixed-mode"></a>混合模式
我们不建议在其中创建接收方使用时期，然后切换到无 epoch 或进行相反转换的相同使用者组上的应用程序使用情况。 但是，如果发生这种行为，服务将使用以下规则进行处理：

- 如果已创建一个使用 Epoch e1 的接收器，并且该接收器正在接收事件；同时，创建的新接收器不带 Epoch，那么，创建新接收器的操作将会失败。 Epoch 接收器始终在系统中优先。
- 如果已创建一个使用 Epoch e1 的接收器，并且该接收器已断开连接；同时，在新 MessagingFactory 中创建的新接收器不带 Epoch，那么，创建新接收器的操作将会成功。 有一点需要此处我们的系统将检测在大约 10 分钟后的"接收方断开连接"。
- 如果创建了一个或多个不带 Epoch 的接收器，并且创建了使用 Epoch e1 的新接收器，那么，所有旧接收器将断开连接。


## <a name="next-steps"></a>后续步骤

熟悉事件处理程序主机后，请参阅以下文章来详细了解事件中心：

* 使用 [事件中心教程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中心编程指南](event-hubs-programming-guide.md)
* [事件中心中的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中心常见问题解答](event-hubs-faq.md)
* [GitHub 上的事件中心示例](https://github.com/Azure/azure-event-hubs/tree/master/samples)
