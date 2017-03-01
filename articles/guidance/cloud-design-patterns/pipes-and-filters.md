---
title: "管道和筛选器模式 | Azure | Microsoft 文档"
description: "将一个执行复杂处理的任务分解为一系列可重复使用的单个元素。"
categories:
- design-implementation
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
ms.openlocfilehash: b4d71ed1c080d63c5873656ac688c9fea788cf17

---
   
# <a name="pipes-and-filters"></a>管道和筛选器

将一个执行复杂处理的任务分解为一系列可重复使用的单个元素。 此模式允许单独部署和缩放执行处理的任务元素，从而可以提高性能、可扩展性和可重用性。

## <a name="context-and-problem"></a>上下文和问题

对于处理的信息，应用程序需要执行复杂性不一的各种任务。 实施应用程序的一种直接但不灵活的方法是将这种处理当作单一模块来执行。 但是，这种方法可能会降低重构代码、优化代码或在应用程序的其他位置需要相同处理的部分时重复使用代码的机会。 

该图说明了使用单一方法处理数据时出现的问题。 应用程序接收并处理来自两个源的数据。 由单个模块处理来自每个源的数据，执行一系列的任务来传送这些数据，然后再将结果传递到应用程序的业务逻辑。 

![图 1 - 使用单个模块实现的解决方案](images/pipes-and-filters-modules.png) 

单个模块执行的某些任务在功能上非常相似，但这些模块已经过单独设计。 实现任务的代码紧密组合在模块中，而且是在几乎没有考虑到重用和可伸缩性的情况下编写而成。 

但是，随着业务需求的更新，由每个模块执行的处理任务或每个任务的部署要求都可能会更改。 某些任务可能是计算密集型的，最好是在功能强大的硬件上运行，而其他任务可能不需要此类昂贵的资源。 此外，将来可能还需要额外的处理，处理执行任务的顺序也可能更改。 我们需要一种解决方案，它既能解决这些问题，又能增加代码重用的可能性。 

## <a name="solution"></a>解决方案

将每个流所需的处理分解为一组单独的组件（或筛选器），每个组件执行一项任务。 通过标准化每个组件接收和发送的数据的格式，这些筛选器可以一起组合到管道中。 这有助于避免代码重复，并在处理要求发生更改的情况下，还可以让删除、替换或集成其他组件的任务变得轻松。 下图显示了使用管道和筛选器实现的解决方案。

![图 2 - 使用管道和筛选器实现的解决方案](images/pipes-and-filters-solution.png) 


处理单个请求所花的时间取决于管道中最慢筛选器的速度。 一个或多个筛选器可能是瓶颈，尤其是在特定数据源的流中出现大量请求时。 管道结构的主要优点是，它提供了为速度缓慢的筛选器运行并行实例的机会，使系统能够分散负载并提高吞吐量。 

构成管道的筛选器可以在不同的计算机上运行，使其能够独立缩放并充分利用许多云环境提供的弹性。 计算密集型筛选器可以在高性能硬件上运行，而其他要求较低的筛选器可以在成本较低的商用硬件上托管。 筛选器甚至不一定要位于同一个数据中心或地理位置，这样管道中的每个元素都可以在靠近自己所需资源的环境中运行。  下图显示了应用于来源 1 的数据管道的示例。 

![图 3 显示了应用于来源 1 的数据管道的示例。](images/pipes-and-filters-load-balancing.png)

如果筛选器的输入和输出采用流结构，则可以为每个筛选器并行执行处理。 管道中的第一个筛选器可以启动其工作并输出其结果，然后在第一个筛选器完成工作之前，按顺序将结果直接传递到下一筛选器。

另一个好处是此模型可以提供复原能力。 如果某一筛选器失败或运行它的计算机不再可用，管道可以重新计划执行筛选器的工作，并将这项工作定向到组件的另一个实例。 一个筛选器失败不一定就会导致整个管道失败。

将管道和筛选器模式与[补偿事务模式](compensating-transaction.md)结合使用，这是实现分布式事务的另一种方法。 分布式事务可以分解为单独的、可补偿的任务，每个任务都可以通过使用筛选器来实现，而筛选器还能实现补偿事务模式。 管道中的筛选器可以作为单独的托管任务来实现，在靠近它们所维护数据的位置运行。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，应考虑以下几点：
- **复杂性**。 此模式提供的较高灵活性也可能会引入复杂性，尤其是管道中的筛选器分布在不同的服务器上时。 

- **可靠性**。 使用基础结构来确保管道中的筛选器之间流动的数据不会丢失。 

- **幂等性**。 如果管道中的筛选器在接收消息后失败，并且将工作重新安排给筛选器的另一个实例，则部分工作可能已经完成。 如果这项工作仅更新全局状态的一些方面（例如存储在数据库中的信息），则可以重复相同的更新。 如果筛选器在将其结果发布到管道中的下一个筛选器后失败，但在指示它已成功完成工作之前，则可能会发生类似的问题。 在这些情况下，相同的工作可能由筛选器的另一个实例重复执行，导致相同的结果发布两次。 这可能会导致管道中的后续筛选器对相同数据处理两次。 因此，应将管道中的筛选器设计为幂等的。 有关详细信息，请参阅 Jonathan Oliver 博客中的 [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/)（幂等模式）。

- **重复消息**。 如果管道中的筛选器在将消息发布到管道的下一阶段后失败，则可能会运行筛选器的另一个实例，并且它会将相同消息的副本发布到管道。 这可能会导致将相同消息的两个实例传递到下一个筛选器。 为了避免这种情况，管道应该检测和消除重复的消息。

    >  如果你通过使用消息队列（例如 Microsoft Azure 服务总线队列）实现管道，则消息队列基础结构可能会提供自动重复消息检测和删除功能。  

- **上下文和状态**。 在管道中，每个筛选器基本上是分开运行的，不应对它的调用方式做任何假设。 这意味着应该为每个筛选器提供足够的上下文来执行其工作。 上下文可以包括大量的状态信息。 

## <a name="when-to-use-this-pattern"></a>何时使用此模式

在以下情况下使用此模式：
- 应用程序所需的处理可以轻松分解为一组独立的步骤。

- 应用程序执行的处理步骤具有不同的可伸缩性要求。

    >  可以将应一起缩放的筛选器分组到同一进程中。 有关详细信息，请参阅[计算资源整合模式](compute-resource-consolidation.md)。 

- 需要具备一定的灵活性，以便能够对应用程序执行的处理步骤重新排序；或需要具备添加和删除步骤的能力。

- 系统可以从将不同步骤的处理能力分配给不同的服务器中获益。

- 需要一种可靠的解决方案，在处理数据的同时，尽可能降低步骤失败带来的影响。 

在以下情况下，此模式可能不起作用：
- 应用程序执行的处理步骤不是独立的，或者它们必须作为同一事务的一部分一起执行。

- 步骤所需的上下文或状态信息量使得该方法效率低下。 它可能会将状态信息保留到数据库，但如果数据库的额外负载导致过度争用资源，请勿使用此策略。

## <a name="example"></a>示例

可以使用一系列消息队列来提供实现管道所需的基础结构。 初始消息队列接收未处理的消息。 作为筛选器任务实现的组件侦听此队列中的消息，执行其工作，然后将转换的消息发布到序列中的下一个队列。 另一个筛选器任务可以侦听此队列中的消息，处理它们，将结果发布到另一个队列等，直到完全转换的数据出现在队列中的最终消息中。 下图说明了如何使用消息队列实现管道。

![图 4 - 使用消息队列实现管道](images/pipes-and-filters-message-queues.png)


如果在 Azure 上构建解决方案，则可以使用服务总线队列来提供可靠和可扩展的排队机制。 C# 中如下所示的 `ServiceBusPipeFilter` 类演示了如何实现从队列接收输入消息的筛选器，处理这些消息以及将结果发布到另一队列。

>  `ServiceBusPipeFilter` 类在可从 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/pipes-and-filters) 获取的 PipesAndFilters.Shared 项目中定义。

```csharp
public class ServiceBusPipeFilter
{
  ...
  private readonly string inQueuePath;
  private readonly string outQueuePath;
  ...
  private QueueClient inQueue;
  private QueueClient outQueue;
  ...

  public ServiceBusPipeFilter(..., string inQueuePath, string outQueuePath = null)
  {
     ...
     this.inQueuePath = inQueuePath;
     this.outQueuePath = outQueuePath;
  }

  public void Start()
  {
    ...
    // Create the outbound filter queue if it doesn't exist.
    ...
    this.outQueue = QueueClient.CreateFromConnectionString(...);
    
    ...
    // Create the inbound and outbound queue clients.
    this.inQueue = QueueClient.CreateFromConnectionString(...);
  }

  public void OnPipeFilterMessageAsync(
    Func<BrokeredMessage, Task<BrokeredMessage>> asyncFilterTask, ...) 
  {
    ...

    this.inQueue.OnMessageAsync(
      async (msg) =>
    {
      ...
      // Process the filter and send the output to the 
      // next queue in the pipeline.
      var outMessage = await asyncFilterTask(msg);

      // Send the message from the filter processor 
      // to the next queue in the pipeline.
      if (outQueue != null)
      {
        await outQueue.SendAsync(outMessage);
      }

      // Note: There's a chance that the same message could be sent twice 
      // or that a message gets processed by an upstream or downstream 
      // filter at the same time.
      // This would happen in a situation where processing of a message was
      // completed, it was sent to the next pipe/queue, and then failed 
      // to complete when using the PeekLock method.
      // Idempotent message processing and concurrency should be considered 
      // in a real-world implementation.
    },
    options);
  }

  public async Task Close(TimeSpan timespan)
  {
    // Pause the processing threads.
    this.pauseProcessingEvent.Reset();

    // There's no clean approach for waiting for the threads to complete
    // the processing. This example simply stops any new processing, waits
    // for the existing thread to complete, then closes the message pump 
    // and finally returns.
    Thread.Sleep(timespan);

    this.inQueue.Close();
    ...
  }

  ...
}
```

`ServiceBusPipeFilter` 类中的 `Start` 方法连接到一对输入和输出队列，`Close` 方法从输入队列中断开连接。 `OnPipeFilterMessageAsync` 方法执行实际的消息处理，此方法的 `asyncFilterTask` 参数指定要执行的处理。 `OnPipeFilterMessageAsync` 方法等待输入队列中的传入消息，在每个消息到达时对其运行 `asyncFilterTask` 参数指定的代码，并将结果发布到输出队列。 队列本身由构造函数指定。 

示例解决方案在一组辅助角色中实现筛选器。 每个辅助角色都可以单独缩放，具体取决于它执行的业务处理的复杂性或处理所需的资源。 此外，可以并行运行每个辅助角色的多个实例以提高吞吐量。 

以下代码显示名为 `PipeFilterARoleEntry` 的 Azure 辅助角色，已在示例解决方案的 PipeFilterA 项目中定义。 

```csharp
public class PipeFilterARoleEntry : RoleEntryPoint
{
  ...
  private ServiceBusPipeFilter pipeFilterA;

  public override bool OnStart()
  {
    ...
    this.pipeFilterA = new ServiceBusPipeFilter(
      ...,
      Constants.QueueAPath,
      Constants.QueueBPath);

    this.pipeFilterA.Start();
    ...
  }

  public override void Run()
  {
    this.pipeFilterA.OnPipeFilterMessageAsync(async (msg) =>
    {
      // Clone the message and update it.
      // Properties set by the broker (Deliver count, enqueue time, ...) 
      // aren't cloned and must be copied over if required.
      var newMsg = msg.Clone();
      
      await Task.Delay(500); // DOING WORK

      Trace.TraceInformation("Filter A processed message:{0} at {1}", 
        msg.MessageId, DateTime.UtcNow);

      newMsg.Properties.Add(Constants.FilterAMessageKey, "Complete");

      return newMsg;
    });

    ...
  }

  ...
}
```

此角色包含 `ServiceBusPipeFilter` 对象。 此角色中的 `OnStart` 方法连接到接收输入消息和发布输出消息的队列（队列名称已在 `Constants` 类中定义）。 `Run` 方法调用 `OnPipeFilterMessagesAsync` 方法，以便对收到的每个消息执行一些处理（在本例中，通过等待一小段时间来模拟处理）。 处理完成时，构造包含结果的新消息（在这种情况下，输入消息已添加自定义属性），并且该消息已被发布到输出队列。

示例代码在 PipeFilterB 项目中包含另一个名为 `PipeFilterBRoleEntry` 的辅助角色。 此角色与 `PipeFilterARoleEntry` 类似，只是它在 `Run` 方法中执行不同的处理。 在示例解决方案中，这两个角色组合构成一个管道，`PipeFilterARoleEntry` 角色的输出队列是 `PipeFilterBRoleEntry` 角色的输入队列。

示例解决方案还提供了两个名为 `InitialSenderRoleEntry`（在 InitialSender 项目中）和 `FinalReceiverRoleEntry`（在 FinalReceiver 项目中）的其他角色。 `InitialSenderRoleEntry` 角色在管道中提供初始消息。 `OnStart` 方法连接到单个队列，`Run` 方法将方法发布到此队列。 此队列是 `PipeFilterARoleEntry` 角色使用的输入队列，因此向其发布消息将使该消息由 `PipeFilterARoleEntry` 角色接收和处理。 然后，处理后的消息将通过 `PipeFilterBRoleEntry` 角色。

`FinalReceiveRoleEntry` 角色的输入队列是 `PipeFilterBRoleEntry` 角色的输出队列。 `FinalReceiveRoleEntry` 角色中的 `Run` 方法（如下所示）接收消息并执行一些最终处理。 然后，它将管道中的筛选器添加的自定义属性值写入到跟踪输出。

```csharp
public class FinalReceiverRoleEntry : RoleEntryPoint
{
  ...
  // Final queue/pipe in the pipeline to process data from.
  private ServiceBusPipeFilter queueFinal;

  public override bool OnStart()
  {
    ...
    // Set up the queue.
    this.queueFinal = new ServiceBusPipeFilter(...,Constants.QueueFinalPath);
    this.queueFinal.Start();
    ...
  }

  public override void Run()
  {
    this.queueFinal.OnPipeFilterMessageAsync(
      async (msg) =>
      {
        await Task.Delay(500); // DOING WORK

        // The pipeline message was received.
        Trace.TraceInformation(
          "Pipeline Message Complete - FilterA:{0} FilterB:{1}",
          msg.Properties[Constants.FilterAMessageKey],
          msg.Properties[Constants.FilterBMessageKey]);

        return null;
      });
    ...
  }

  ...
}
```

##<a name="related-patterns-and-guidance"></a>相关模式和指南

实现此模式时，可能也会与以下模式和指南相关：
- 演示此模式的示例可在 [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/pipes-and-filters) 上找到。
- [使用者竞争模式](competing-consumers.md)。 管道可以包含一个或多个筛选器的多个实例。 此方法可用于为速度缓慢的筛选器运行并行实例，使系统能够分散负载并提高吞吐量。 每个筛选器实例将与其他实例争用输入，筛选器的两个实例不能处理相同的数据。 提供此方法的说明。
- [计算资源整合模式](compute-resource-consolidation.md)。 可以将应一起缩放的筛选器分组到同一进程中。 提供有关此策略的优点和缺点的详细信息。
- [补偿事务模式](compensating-transaction.md)。 筛选器可以作为可逆转的操作、或在发生故障时将状态还原到先前版本的补偿操作来实现。 解释如何实现上述目的来维持或达到最终的一致性。 
- Jonathan Oliver 博客中的 [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/)（幂等模式）。 



<!--HONumber=Nov16_HO3-->


