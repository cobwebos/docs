---
title: "Durable Functions 中的检查点和重播 - Azure"
description: "了解 Azure Functions 的 Durable Functions 扩展中的检查点和重播工作原理。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 28c589b6821526fa6b91dc558a08ef2fb68f97df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Durable Functions 中的检查点和重播 (Azure Functions)

Durable Functions 的关键属性之一是**可靠执行**。 业务流程协调程序函数和活动函数可在数据中心内的不同 VM 上运行，而这些 VM 或底层网络基础结构并不是 100% 可靠。

尽管如此，Durable Functions 能够确保可靠执行业务流程。 为此，它会使用存储队列来驱动函数调用，并定期在存储表中创建执行历史记录的检查点（使用称作[事件寻源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)的云设计模式）。 然后可以重播该历史记录，以便自动重新生成业务流程协调程序函数的内存中状态。

## <a name="orchestration-history"></a>业务流程历史记录

假设存在以下业务流程协调程序函数。

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

执行到每条 `await` 语句时，Durable Task Framework 会在表存储中创建该函数的执行状态检查点。 此状态称为“业务流程历史记录”。

## <a name="history-table"></a>历史记录表

一般而言，Durable Task Framework 会在每个检查点位置执行以下操作：

1. 将执行历史记录保存到 Azure 存储表中。
2. 将业务流程协调程序想要调用的函数的相关消息排队。
3. 将业务流程协调程序本身的消息排队 &mdash; 例如，持久计时器消息。

完成检查点之后，在业务流程协调程序函数需要执行其他工作之前，可以从内存中任意删除该函数。

> [!NOTE]
> 在将数据保存到表存储和队列的间隔期限内，Azure 存储不提供任何事务保证。 若要处理错误，Durable Functions 存储提供程序会使用“最终一致性”模式。 这些模式可确保在发生崩溃时不会丢失数据，或者在创建检查点的中途不会断开连接。

完成后，前面所示的函数历史记录在 Azure 表存储中如下所示（为方便演示，此处采用了缩写）：

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | 输入 | Name             | 结果                                                    | 状态 | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Null  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | 已完成           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

有关列值的一些注释：
* **PartitionKey**：包含业务流程的实例 ID。
* **EventType**：表示事件的类型。 可为以下类型之一：
    * **OrchestrationStarted**：业务流程协调程序函数已从 await（等待）状态恢复，或者正在首次运行。 `Timestamp` 列用于填充 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API 的确定性值。
    * **ExecutionStarted**：业务流程协调程序函数已开始首次执行。 此事件也包含 `Input` 列中输入的函数。
    * **TaskScheduled**：已计划活动函数。 `Name` 列中已捕获该活动函数的名称。
    * **TaskCompleted**：已完成活动函数。 `Result` 列中提供了该函数的结果。
    * **TimerCreated**：已创建持久计时器。 `FireAt` 列包含计时器过期时的 UTC 计划时间。
    * **TimerFired**：持久计时器已过期。
    * **EventRaised**：已将外部事件发送到业务流程实例。 `Name` 列捕获事件的名称，`Input` 列捕获事件的有效负载。
    * **OrchestratorCompleted**：业务流程协调程序函数处于等待状态。
    * **ContinueAsNew**：业务流程协调程序函数已完成，并已使用新状态重启自身。 `Result` 列包含用作已重启实例中的输入的值。
    * **ExecutionCompleted**：业务流程协调程序函数已运行并已完成（或失败）。 该函数的输出或错误详细信息存储在 `Result` 列中。
* **Timestamp**：历史记录事件的 UTC 时间戳。
* **Name**：调用的函数的名称。
* **Input**：函数的 JSON 格式输入。
* **Output**：函数的输出，即其返回值。

> [!WARNING]
> 尽管此表可以用作有效的调试工具，但不要对它有任何依赖。 它可能会随着 Durable Functions 扩展的演变而变化。

每当函数从 `await` 状态恢复时，Durable Task Framework 会从头开始重新运行业务流程协调程序函数。 每次重新运行时，它会查询执行历史记录，确定当前的异步操作是否已发生。  如果该操作已发生，该框架会立即重播该操作的输出，并转到下一个 `await`。 此过程会持续到整个历史记录被重播为止，此后，业务流程协调程序函数中的所有本地变量会还原到其先前值。

## <a name="orchestrator-code-constraints"></a>业务流程协调程序代码约束

重播行为针对可在业务流程协调程序中编写的代码类型创建约束。

* 业务流程协调程序代码必须是**确定性的**。 该代码将被重播多次，每次必须生成相同的结果。 例如，不能通过直接调用来获取当前日期/时间、获取随机数、生成随机 GUID 或调入远程终结点。

  如果业务流程协调程序代码需要获取当前日期/时间，应使用可安全重播的 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) API。

  不确定性的操作必须在活动函数中执行。 这包括与其他输入或输出绑定之间的任何交互。 这可以确保在完成首次执行之后立即生成所有不确定性值并将其保存到执行历史记录。 然后，后续执行会自动使用保存的值。

* 业务流程协调程序代码应是**非阻塞性的**。 例如，不能使用 `Thread.Sleep` 或等效的 API。

  如果业务流程协调程序需要延迟，可以使用 [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) API。

* 除非使用 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API，否则业务流程协调程序**不得发起任何异步操作**。 例如，不能使用 `Task.Run`、`Task.Delay` 或 `HttpClient.SendAsync`。 Durable Task Framework 在单个线程上执行业务流程协调程序代码，不能与可由其他异步 API 计划的其他任何线程交互。

* 在业务流程协调程序代码中，**应避免无限循环**。 由于 Durable Task Framework 在业务流程函数的执行过程中会保存执行历史记录，无限循环可能会导致业务流程协调程序实例耗尽内存。 对于无限循环方案，可使用 [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) 等 API 来重启函数执行，并丢弃以前的执行历史记录。

尽管这些约束在乍看之下让人心虚，但其实并不难遵守。 Durable Task Framework 会尝试检测上述规则的冲突，并引发 `NonDeterministicOrchestrationException`。 但是，这种检测行为是尽力而为的，请不要对它有依赖。

> [!NOTE]
> 所有这些规则只会应用到 `orchestrationTrigger` 绑定触发的函数。 `activityTrigger` 绑定触发的活动函数以及使用 `orchestrationClient` 绑定的函数没有此类限制。

## <a name="durable-tasks"></a>持久任务

> [!NOTE]
> 本部分介绍 Durable Task Framework 的内部实现详细信息。 在不了解这些信息的情况下也可以使用 Durable Functions。 本部分旨在帮助读者了解重播行为。

可在业务流程协调程序函数中安全等待的任务有时称为“持久任务”。 这些任务由 Durable Task Framework 创建和管理。 示例包括 `CallActivityAsync`、`WaitForExternalEvent` 和 `CreateTimer` 返回的任务。

使用 `TaskCompletionSource` 对象的列表在内部管理这些持久任务。 在重播期间，这些任务在业务流程协调程序代码执行过程中予以创建，并在调度程序枚举相应历史记录事件时完成。 在重播所有历史记录之前，所有这些操作会通过单个线程以同步方式完成。 针对在历史记录重播结束时尚未完成的任何持久任务，将会执行相应的操作。例如，可能会将一条消息排队，以调用活动函数。

借助此处所述的执行行为，我们应该可以理解业务流程协调程序函数代码为何不得将非持久任务置于 `await` 状态：调度程序线程无法等待该任务完成，并且该任务发出的任何回调可能会破坏业务流程协调程序函数的跟踪状态。 一些运行时检查可以尽量防止发生这种情况。

如需有关 Durable Task Framework 如何执行业务流程协调程序函数的详细信息，最好是查阅 [GitHub 上的持久任务源代码](https://github.com/Azure/durabletask)。 具体而言，请参阅 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 和[TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解实例管理](durable-functions-instance-management.md)