---
title: "Azure 服务总线消息传输、锁定和处置 | Microsoft Docs"
description: "服务总线消息传输和处置操作概述"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: edb6e207852fa59d5828906c891693f367739c9c
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="message-transfers-locks-and-settlement"></a>消息传输、锁定和处置

消息代理（如服务总线）的最核心功能是将消息接受到队列或主题中以及保存它们以用于将来检索。 *发送*是常用于指消息传输到消息代理中的术语。 *接收*是常用于指将消息传输到检索客户端的术语。

当客户端发送消息时，它通常希望了解消息是否正确传输到代理并由代理接受，或是否发生某种形式的错误。 这种肯定或否定确认会使客户端和代理了解消息传输状态，因而称为。

同样，当中转站向客户端传输消息时，中转站和客户端都希望了解消息是已成功处理（因而可以删除消息），还是消息传递或处理失败（因而可能需要再次传递消息）。

## <a name="settling-send-operations"></a>处置发送操作

使用任何支持的服务总线 API 客户端时，到服务总线中的发送操作会始终进行显式处置，这意味着 API 操作会等待来自服务总线的接受结果到达，然后完成发送操作。

如果服务总线拒绝消息，则拒绝包含错误指示器以及其中带有“tracking-id”的文本。 拒绝还包含有关是否可以在期望成功的情况下重试操作的信息。 在客户端中，此信息会转变为异常并引发到发送操作的调用方。 如果接受了消息，则操作会以无提示方式完成。

使用 AMQP 协议（这是用于 .NET 标准客户端和 Java 客户端的独有协议，并且[是用于 .NET Framework 客户端的选项](service-bus-amqp-dotnet.md)）时，消息传输和处置会通过管道传递并完全异步，建议使用异步编程模型 API 变体。

发送方可以快速连续地将多个消息置于线路上，而不必等待确认每个消息（使用 SBMP 协议或使用 HTTP 1.1 时会是这种情况）。 当在分区实体上接受和存储相应消息时，或是在到不同实体的发送操作重叠时，这些异步发送操作会完成。 完成也可能不会按原始发送顺序进行。

用于处理发送操作结果的策略可能会对应用程序形成直接的显著性能影响。 此部分中的示例使用 C# 编写，对于 Java Future 同样适用。

如果应用程序会产生消息突发（此处使用普通循环说明），并且会等待每个发送操作完成，然后再发送下一个消息，则同步或异步 API 的情况相似，发送 10 个消息只会在 10 个连续的完整处置往返之后完成。

假设从本地站点到服务总线有 70 毫秒 TCP 往返延迟距离，并且服务总线接受并存储每个消息只需 10 毫秒，则以下循环花费至少 8 秒（不考虑有效负载传输时间或潜在路由拥塞影响）：

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

如果应用程序即时连续地启动 10 个异步发送操作，并分别等待其各自完成，则这 10 个发送操作的往返时间会重叠。 10 个消息会即时连续传输（甚至可能会共享 TCP 帧），总体传输持续时间在很大程度上取决于使消息传输到代理的网络相关时间。

进行与前面循环相同的假设，以下循环的总重叠执行时间可能刚好低于一秒：

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

请务必注意，所有异步编程模型都使用某种形式的基于内存的隐藏工作队列来保存挂起的操作。 当 [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) 或 **Send** (Java) 返回时，发送任务会在该工作队列中进行排队，但协议操作只会在轮到任务运行之后开始。 对于倾向于推送消息突发并且需要考虑可靠性的代码，应注意不要同时“发送”太多消息，因为所有发送的消息在实际置于线路上之前都会占用内存。

如以下 C# 代码片段中所示，信号灯是可在需要时启用这类应用程序级别限制的同步对象。 信号灯的这种用法允许同时最多发送 10 个消息。 10 个可用信号灯锁中的一个会在发送之前采用，然后在发送完成时释放。 循环的第 11 次执行会等待以前发送中的至少一个发送完成，然后使其锁可用：

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

应用程序**绝不**应采用“发后不理”方式启动异步发送操作，而不检索操作结果。 这样做可以加载内部和不可见任务队列，直到内存耗尽，并阻止应用程序检测发送错误：

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

对于低级别 AMQP 客户端，服务总线还接受“预处置”传输。 预处置传输是发后不理操作，其结果在任一情况下都不会报告回客户端并且消息在发送时被视为已处置。 不向客户端进行反馈还意味着没有任何可操作数据可用于诊断，这表示此模式没有资格通过 Azure 支持获得帮助。

## <a name="settling-receive-operations"></a>处置接收操作

对于接收操作，服务总线 API 客户端启用两种不同的显式模式：接收并删除和扫视锁定。

[接收并删除](/dotnet/api/microsoft.servicebus.messaging.receivemode)模式告知代理将它发送到接收客户端的所有消息都在发送时视为已处置。 这意味着在代理将消息置于线路上之后，它会立即被视为已使用。 如果消息传输失败，则消息会丢失。

此模式的优点是接收方无需对消息执行进一步操作，也不会由于等待处置结果而减慢速度。 如果各个消息中包含的数据具有较低值并且/或者只在很短时间内才有意义，则此模式是合理选择。

[扫视锁定](/dotnet/api/microsoft.servicebus.messaging.receivemode)模式告知代理接收客户端希望显式处置收到的消息。 消息可供接收方进行处理，同时在服务中保持在排他锁下，以便其他竞争接收方无法看到它。 该锁的持续时间最初在队列或订阅级别进行定义，可以由拥有该锁的客户端通过 [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) 操作进行延长。

锁定消息时，从相同队列或订阅进行接收的其他客户端可以接受锁并检索不处于活动锁下的下一个可用消息。 显式释放消息上的锁或是锁过期时，消息会弹回到检索顺序前列或附近，以便重新传递。

当接收方重复释放消息或是使锁结束定义的次数 ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)) 时，消息会自动从队列或订阅中删除并放入关联死信队列中。

当接收客户端在 API 级别调用 [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) 时，会使用肯定确认启动收到的消息的处置。 这会向代理指出消息已成功处理，并且消息会从队列或订阅中删除。 代理会使用指示是否可以执行处置的回复来回复接收者的处置意向。

如果接收客户端未能处理消息，但是希望重新传递消息，则可以通过调用 [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) 来显式要求立即释放并解锁消息，也可以不执行任何操作，让锁结束。

如果接收客户端未能处理消息并且知道重新传递消息并重试操作将不起作用，则可以拒绝消息，这会通过调用 [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter) 将它移动到死信队列中，从而还允许设置自定义属性（包括可以对来自死信队列的消息检索的原因代码）。

处置的一种特殊情况是延迟，这会在单独文章中进行讨论。

如果持有的锁已过期，或是存在阻止处置的其他服务端条件，则 **Complete** 或 **Deadletter** 操作以及 **RenewLock** 操作可能会由于网络问题而失败。 在后面一种情况下，服务会发送否定确认，该确认会在 API 客户端中表现为异常。 如果原因是网络连接中断，则会丢弃锁，因为服务总线不支持在不同连接上恢复现有 AMQP 链接。

如果 **Complete** 失败（这通常在消息处理结束时发生，在某些情况下会在处理工作进行几分钟之后发生），则接收应用程序可以决定是否在第二次传递时保留工作状态并忽略相同消息，或是否在重新传递消息时丢弃工作结果并重试。

用于标识重复消息传递的典型机制是检查消息 ID，它可以并且应该由发送方设置为唯一值（可能与来自发起进程的标识符一致）。 作业计划程序可能会将消息 ID 设置为它尝试通过给定辅助进程分配给辅助进程的作业的标识符，并且该辅助进程会在作业已完成时忽略该作业的第二次出现。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
