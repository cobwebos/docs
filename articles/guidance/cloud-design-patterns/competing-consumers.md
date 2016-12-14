---
title: "使用者竞争模式 | Azure | Microsoft Docs"
description: "使多个并发使用者能够处理同一消息通道上收到的消息。"
categories:
- messaging
keywords: "设计模式"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 420f94210728a4707d7b2d488eb87b8046fa344b

---
   
# <a name="competing-consumers"></a>让使用者竞争

使多个并发使用者能够处理同一消息通道上收到的消息。 它可让系统同时处理多个消息，以优化吞吐量、改进可扩展性和可用性，以及平衡工作负荷。

## <a name="context-and-problem"></a>上下文和问题

在云中运行的应用程序需要处理大量的请求。 常用方法不是同步处理每个请求，而是应用程序通过消息传递系统将它们传送到异步处理它们的另一个服务（使用者服务）。 此策略有助于确保在处理请求时应用程序中的业务逻辑不会被阻止。

在一段时间内，由于多种原因请求的数量会大幅度变化。 用户活动或来自多个租户的总请求数的突增可能会导致不可预测的工作负荷。 在高峰时间，系统可能需要每秒处理数百个请求，而在其他时间，请求的数量可能非常少。 此外，为处理请求而执行的工作的性质可能会有很大变化。 运行使用者服务的单个实例可能导致该实例充满请求，或者消息系统可能由于来自应用程序的消息涌入而过载。 为了处理这种波动的工作负荷，系统可以运行使用者服务的多个实例。 但是，这些使用者必须进行协调以确保每条消息仅传送给一个使用者。 工作负荷还需要在使用者之间处于负载平衡状态，以防止实例成为瓶颈。

## <a name="solution"></a>解决方案

使用消息队列来实现应用程序和使用者服务实例之间的信道。 应用程序以消息的形式将请求发送到队列，使用者服务实例从队列接收消息并进行处理。 此方法可让使用者服务实例的相同池处理来自应用程序实例的消息。 该图说明了如何使用消息队列将工作分布到服务实例。

![使用消息队列将工作分布到服务实例](images/compensating-transaction-diagram.png)

此解决方案具有以下优点：

- 它提供了一个负载调节系统，可以处理应用程序实例发送的大幅度变化的请求量。 队列充当应用程序实例和使用者服务实例之间的缓冲区。 这有助于尽量减少对应用程序和服务实例的可用性和响应性的影响，如[基于队列的负载调节模式](queue-based-load-leveling.md)中所述。 处理需要长时间运行处理的消息时不会阻止使用者服务的其他实例同时处理其他消息。

- 它提高了可靠性。 如果生成者直接与使用者通信，而不使用这种模式且不对使用者进行监视，则消息很可能丢失或未能处理（如果使用者失败）。 在此模式中，消息不会发送到特定服务实例。 失败的服务实例不会阻止生成者，并且任何工作服务实例都可处理消息。

- 它不需要使用者之间或生成者与使用者实例之间的复杂协调。 消息队列可确保每条消息至少传送一次。

- 可缩放。 消息数量波动时，系统可以动态地增加或减少使用者服务实例的数量。

- 如果消息队列提供事务读取操作，则可以提高复原能力。 如果使用者服务实例读取和处理消息（作为事务操作的一部分），并且使用者服务实例失败，则该模式可以确保消息将返回到队列由另一使用者服务实例进行选取并处理。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

- **消息排序**。 不能保证使用者服务实例接收消息的顺序，且不一定反映创建消息的顺序。 设计系统以确保消息处理是幂等的，因为这有助于消除对消息处理顺序的任何依赖。 有关详细信息，请参阅 Jonathon Oliver 博客中的 [Idempotency Patterns](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/)（幂等模式）。

    > Microsoft Azure 服务总线队列可通过消息会话对消息执行保证的先进先出顺序。 有关详细信息，请参阅[使用会话的消息传送模式](https://msdn.microsoft.com/magazine/jj863132.aspx)。

- **为复原能力设计服务**。 如果系统专用于检测和重新启动失败的服务实例，则可能需要实现由服务实例作为幂等操作执行的处理，以尽量减少对多次检索和处理的单个消息的影响。

- **检测有害消息**。 格式不正确的消息或需要访问不可用资源的任务可能会导致服务实例失败。 系统应阻止此类消息返回队列，并在其他位置捕获和存储这些消息的详细信息，以便在必要时对其进行分析。

- **处理结果**。 处理消息的服务实例与生成消息的应用程序逻辑完全分离，它们可能无法直接通信。 如果服务实例生成必须传递回应用程序逻辑的结果，则此信息必须存储在两者都可访问的位置。 为了防止应用程序逻辑检索不完整的数据，系统必须在处理完成时指示。

     > 如果使用的是 Azure，工作进程可使用专用消息答复队列将结果传回应用程序逻辑。 应用程序逻辑必须能够将这些结果与原始消息相关联。 有关此方案的详细信息，请参阅 [Asynchronous Messaging Primer](https://msdn.microsoft.com/library/dn589781.aspx)（异步消息传送入门）。
     
- **缩放消息传送系统**。 在大规模解决方案中，单个消息队列可能不堪应付太多的请求，并且在系统中成为瓶颈。 在这种情况下，请考虑对消息系统进行分区以将消息从特定生成者发送到特定队列，或者使用负载平衡在多个消息队列之间分发消息。

- **确保消息传送系统的可靠性**。 需要可靠的消息传递系统来保证在应用程序将消息放入队列之后它不会丢失。 这对于确保所有消息至少传送一次至关重要。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

在以下情况下使用此模式：

- 应用程序的工作负荷分为可以异步运行的任务。
- 任务是独立的且可并行运行。
- 工作量是多变的，因此需要可缩放的解决方案。
- 该解决方案必须提供高可用性，并且如果任务的处理失败，必须具有复原能力。

在以下情况下，此模式可能不起作用：

- 难以将应用程序工作负荷分成离散任务，或任务之间存在高度依赖性。
- 任务必须同步执行，且应用程序逻辑必须等待任务完成后才能继续。
- 必须以特定顺序执行任务。

> 某些消息传递系统支持会话，使生成者能够将消息组合在一起，并确保由相同的使用者进行处理。 此机制可用于按优先级排列的消息（如果支持）以实现消息排序的形式，从生成者到单个使用者按顺序传送消息。

## <a name="example"></a>示例

Azure 提供存储队列和服务总线队列，它们可用作实现此模式的机制。 应用程序逻辑可将消息发布到队列，并且作为一个或多个角色中的任务实现的使用者可以从该队列检索消息并进行处理。 对于复原能力，使用者可借助服务总线队列在从队列检索消息时使用 `PeekLock` 模式。 此模式不会真正删除消息，而只是对其他使用者隐藏消息。 原始使用者可在完成处理后删除消息。 如果使用者失败，扫视锁定将超时，该消息将再次可见且允许其他使用者检索它。

> 有关如何使用 Azure 服务总线队列的详细信息，请参阅 [Service Bus Queues, Topics, and Subscriptions](https://msdn.microsoft.com/library/windowsazure/hh367516.aspx)（服务总线队列、主题和订阅）。 有关如何使用 Azure 存储队列的信息，请参阅[通过 .NET 开始使用 Azure 队列存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/)。

在 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/competing-consumers) 上可用的 CompetingConsumers 解决方案中 `QueueManager` 类的以下代码演示了如何使用 Web 或 辅助角色中 `Start` 事件处理程序的 `QueueClient` 实例创建队列。

```csharp
private string queueName = ...;
private string connectionString = ...;
...

public async Task Start()
{
  // Check if the queue already exists.
  var manager = NamespaceManager.CreateFromConnectionString(this.connectionString);
  if (!manager.QueueExists(this.queueName))
  {
    var queueDescription = new QueueDescription(this.queueName);

    // Set the maximum delivery count for messages in the queue. A message 
    // is automatically dead-lettered after this number of deliveries. The
    // default value for dead letter count is 10.
    queueDescription.MaxDeliveryCount = 3;

    await manager.CreateQueueAsync(queueDescription);
  }
  ...

  // Create the queue client. By default the PeekLock method is used.
  this.client = QueueClient.CreateFromConnectionString(
    this.connectionString, this.queueName);
}
```

下一个代码片段演示了应用程序如何创建批量消息并将其发送到队列。

```csharp
public async Task SendMessagesAsync()
{
  // Simulate sending a batch of messages to the queue.
  var messages = new List<BrokeredMessage>();

  for (int i = 0; i < 10; i++)
  {
    var message = new BrokeredMessage() { MessageId = Guid.NewGuid().ToString() };
    messages.Add(message);
  }
  await this.client.SendBatchAsync(messages);
}
```

以下代码显示了使用者服务实例如何通过事件驱动方法从队列接收消息。 `ReceiveMessages` 方法的 `processMessageTask` 参数是一个委托，其引用要在接收到消息时运行的代码。 以异步方式运行此代码。

```csharp
private ManualResetEvent pauseProcessingEvent;
...

public void ReceiveMessages(Func<BrokeredMessage, Task> processMessageTask)
{
  // Set up the options for the message pump.
  var options = new OnMessageOptions();

  // When AutoComplete is disabled it's necessary to manually
  // complete or abandon the messages and handle any errors.
  options.AutoComplete = false;
  options.MaxConcurrentCalls = 10;
  options.ExceptionReceived += this.OptionsOnExceptionReceived;

  // Use of the Service Bus OnMessage message pump. 
  // The OnMessage method must be called once, otherwise an exception will occur.
  this.client.OnMessageAsync(
    async (msg) =>
    {
      // Will block the current thread if Stop is called.
      this.pauseProcessingEvent.WaitOne();

      // Execute processing task here.
      await processMessageTask(msg);
    },
    options);
}
...

private void OptionsOnExceptionReceived(object sender, 
  ExceptionReceivedEventArgs exceptionReceivedEventArgs)
{
  ...
}
```

请注意，自动缩放功能（例如 Azure 中提供的功能）可用于在队列长度波动时启动和停止角色实例。 有关详细信息，请参阅 [Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx)（自动缩放指南）。 此外，没有必要在角色实例和工作进程之间保持一一对应的关系 &mdash; 单个角色实例可执行多个工作进程。 有关详细信息，请参阅[计算资源整合模式](compute-resource-consolidation.md)。

## <a name="related-patterns-and-guidance"></a>相关模式和指南

实现此模式时，可能会与以下模式和指南相关：

- [异步消息传送入门](https://msdn.microsoft.com/library/dn589781.aspx)。 消息队列是异步通信机制。 如果使用者服务需要向应用程序发送回复，则可能需要实现某种形式的响应消息传送。 异步消息传送入门提供了有关如何使用消息队列实现请求/回复消息传送的信息。
 
- [Autoscaling Guidance](https://msdn.microsoft.com/library/dn589774.aspx)（自动缩放指南）。 由于队列应用程序发布消息的长度不同，因此可能可以启动和停止使用者服务的实例。 自动缩放有助于在峰值处理期间保持吞吐量。

- [计算资源整合模式](compute-resource-consolidation.md)。 可以将使用者服务的多个实例整合到单个进程以降低成本和管理开销。 计算资源整合模式说明了遵循这种方法的利弊。

- [基于队列的负载调节模式](queue-based-load-leveling.md)。 引入消息队列可以为系统添加复原能力，使服务实例能够处理应用程序实例发送的大幅度变化的请求量。 消息队列可作为缓冲区，用于调节负载。 有关此方案的详细信息，请参阅基于队列的负载调节模式。

- 此模式具有相关联的[示例应用程序](https://github.com/mspnp/cloud-design-patterns/tree/master/competing-consumers)。



<!--HONumber=Nov16_HO3-->


