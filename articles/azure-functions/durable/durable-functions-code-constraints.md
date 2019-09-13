---
title: 持久性 orchestrator 代码约束-Azure Functions
description: Azure Durable Functions 的业务流程函数重播和代码约束。
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935763"
---
# <a name="orchestrator-function-code-constraints"></a>业务流程协调程序函数代码约束

Durable Functions 是[Azure Functions](../functions-overview.md)的扩展，可让你构建有状态的应用程序。 您可以使用业务流程协调程序[函数](durable-functions-orchestrations.md)在 function app 中协调其他持久函数的执行。 业务流程协调程序函数有状态、可靠且可能长时间运行。

## <a name="orchestrator-code-constraints"></a>业务流程协调程序代码约束

业务流程协调程序函数使用[事件源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)来确保可靠执行并保持本地变量状态。 Orchestrator 代码的[重播行为](durable-functions-orchestrations.md#reliability)针对可在业务流程协调程序函数中编写的代码类型创建约束。 例如，orchestrator 代码必须是*确定性*的。  业务流程协调程序函数将多次重播，每次都必须生成相同的结果。

以下部分提供了一些简单的准则，可确保代码是确定性的。

### <a name="using-deterministic-apis"></a>使用确定性 Api

业务流程协调程序函数可以随意调用其目标语言中所需的任何 API。 但是，业务流程协调程序函数只调用*确定*的 api，这一点很重要。 *确定性 api*是指在给定相同输入的情况下始终返回完全相同的值，无论调用的时间或频率如何。

下面是应避免的 Api 的一些示例，因为它们*不*是确定性的。 这些限制仅适用于业务流程协调程序函数。 其他函数类型没有此类限制。

| API 类别 | Reason | 解决方法 |
| ------------ | ------ | ---------- |
| 日期/时间  | 返回_当前_日期或时间的 api 是非确定性的，因为它们返回的值对于每次重播都是不同的。 | 使用可安全重播的[CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) （ `currentUtcDateTime` .net）或（JavaScript） API。 |
| Guid/Uuid  | 返回_随机_GUID/UUID 的 api 是不确定性的，因为每次重播生成的值都是不同的。 | 使用[NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) （.net）或`newGuid` （JavaScript）来安全地生成随机 guid。 |
| 随机数字 | 返回随机数的 Api 是不确定性的，因为每次重播生成的值都是不同的。 | 使用活动函数可将随机数值返回给业务流程。 活动函数的返回值对于重播始终是安全的。 |
| 绑定 | 输入和输出绑定通常执行 i/o 操作且不具有确定性。 即使[业务流程客户端](durable-functions-bindings.md#orchestration-client)和[实体客户端](durable-functions-bindings.md#entity-client)绑定也不能由 orchestrator 函数直接使用。 | 在客户端或活动函数内使用输入绑定和输出绑定。 |
| 网络 | 网络调用涉及外部系统并且是非确定性的。 | 使用活动函数进行网络调用。 如果需要从业务流程协调程序函数发出 HTTP 调用，还可以选择使用[持久性 HTTP api](durable-functions-http-features.md#consuming-http-apis)。 |
| 阻塞 Api | 阻塞 api （如`Thread.Sleep` （.net）或其他类似的 api）可能会导致 orchestrator 函数的性能和规模出现问题，应避免这样做。 在 Azure Functions 消耗计划中，它们甚至会导致不必要的执行时费用。 | 使用替代项来阻止 api （如`CreateTimer` ），以便在执行业务流程时引入延迟。 [持久计时器](durable-functions-timers.md)延迟不计入业务流程协调程序函数的执行时间。 |
| 异步 Api | 除非使用 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API 或 `context.df` 对象的 API，否则业务流程协调程序不得发起任何异步操作。 例如，.NET 中没有 `Task.Run`、`Task.Delay` 或 `HttpClient.SendAsync`，JavaScript 中没有 `setTimeout()` 和 `setInterval()`。 Durable Task Framework 在单个线程上执行业务流程协调程序代码，不能与可由其他异步 API 计划的其他任何线程交互。 | 只有*可持续*异步调用应由业务流程协调程序函数进行。 任何其他异步 API 调用都应从活动函数执行。 |
| 异步 JavaScript 函数 | JavaScript orchestrator 函数不能`async`是因为 node.js 运行时不保证异步函数是确定性的。 | JavaScript orchestrator 函数必须声明为同步生成器函数。 |
| 线程 Api | 持久性任务框架在单个线程上执行 orchestrator 代码，不能与任何其他线程交互。 向业务流程的执行引入新线程可能会导致不确定的执行或死锁。 | 线程 Api 几乎不应在业务流程协调程序函数中使用。 如果需要，它们应限制为活动函数。 |
| 静态变量 | 应避免在业务流程协调程序函数中采用非常量静态变量，因为它们的值可能随时间而变化，从而导致非确定性的执行行为。 | 使用常量或将静态变量限制为活动函数。 |
| 环境变量 | 不要在业务流程协调程序函数中使用环境变量。 它们的值可能会随时间而变化，从而导致非确定性的执行行为。 | 环境变量只能在客户端函数或活动函数内引用。 |
| 无限循环 | 应避免在业务流程协调程序函数中出现无限循环。 持久任务框架可在业务流程函数进行过程中保存执行历史记录，因此无限循环可能会导致 orchestrator 实例用尽内存。 | 对于无限循环方案，可使用 [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) 或 `continueAsNew` (JavaScript) 等 API 来重启函数执行，并丢弃以前的执行历史记录。 |

尽管这些约束在乍看之下让人心虚，但其实并不难遵守。 Durable Task Framework 会尝试检测上述规则的冲突，并引发 `NonDeterministicOrchestrationException`。 但是，这种检测行为是尽力而为的，请不要对它有依赖。

## <a name="versioning"></a>版本控制

持久的业务流程可能会持续的时间、月、年甚至[永久](durable-functions-eternal-orchestrations.md)。 对影响尚未完成的业务流程的 Durable Functions 应用程序进行的任何代码更新都可能会破坏其重播行为。 因此，在对代码进行更新时，请务必仔细进行规划。 有关如何对代码进行版本管理的更详细说明，请参阅[版本管理](durable-functions-versioning.md)一文。

## <a name="durable-tasks"></a>持久任务

> [!NOTE]
> 本部分介绍 Durable Task Framework 的内部实现详细信息。 在不了解这些信息的情况下也可以使用 Durable Functions。 本部分旨在帮助读者了解重播行为。

可在业务流程协调程序函数中安全等待的任务有时称为“持久任务”。 这些任务由持久性任务框架创建和管理。 例如`CallActivityAsync`，.net orchestrator 函数中的`WaitForExternalEvent`、和`CreateTimer`返回的任务。

这些*持久任务*在内部通过使用 .net 中的`TaskCompletionSource`对象列表进行管理。 在重播期间，这些任务在业务流程协调程序代码执行过程中予以创建，并在调度程序枚举相应历史记录事件时完成。 使用单个线程同步执行，直到重播所有历史记录。 在历史记录重播结束时未完成的任何持久任务都将执行相应的操作。例如，可能会将一条消息排队，以调用活动函数。

此处所述的执行行为应有助于理解 orchestrator 函数代码必须从不`await`或`yield`非持久任务的原因：调度程序线程无法等待它完成，并且该任务的任何回调都可能损坏了业务流程协调程序函数的跟踪状态。 某些运行时检查已准备就绪，可以尝试检测这些冲突。

如需有关 Durable Task Framework 如何执行业务流程协调程序函数的详细信息，最好是查阅 [GitHub 上的持久任务源代码](https://github.com/Azure/durabletask)。 具体而言，请参阅 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 和[TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何调用子业务流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)
