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
ms.openlocfilehash: 66e33874c3cfe8f9d2594489b3165f81b2ce84ab
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694817"
---
# <a name="orchestrator-function-code-constraints"></a>业务流程协调程序函数代码约束

Durable Functions 是[Azure Functions](../functions-overview.md)的扩展，可让你构建有状态的应用程序。 您可以使用业务流程协调程序[函数](durable-functions-orchestrations.md)在 function app 中协调其他持久函数的执行。 业务流程协调程序函数有状态、可靠且可能长时间运行。

## <a name="orchestrator-code-constraints"></a>业务流程协调程序代码约束

业务流程协调程序函数使用[事件源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)来确保可靠执行并保持本地变量状态。 Orchestrator 代码的[重播行为](durable-functions-orchestrations.md#reliability)针对可在业务流程协调程序函数中编写的代码类型创建约束。 例如，业务流程协调程序函数必须是*确定性*的：业务流程协调程序函数将多次重播，每次必须生成相同的结果。

### <a name="using-deterministic-apis"></a>使用确定性 Api

本节提供一些简单的指导原则，可帮助确保您的代码是确定性的。

业务流程协调程序函数可以在其目标语言中调用任何 API。 但是，业务流程协调程序函数只调用确定性 Api，这一点很重要。 *确定性 api*是在给定相同输入的情况下始终返回相同值的 api，无论调用的时间或频率如何。

下表显示了应避免的 Api 示例，因为它们*不*是确定性的。 这些限制仅适用于业务流程协调程序函数。 其他函数类型没有此类限制。

| API 类别 | Reason | 解决方法 |
| ------------ | ------ | ---------- |
| 日期和时间  | 返回当前日期或时间的 Api 是不确定性的，因为每次重播返回的值不同。 | 在 .NET 中使用[**CurrentUtcDateTime**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) api，或在 JavaScript 中使用**CurrentUtcDateTime** api，这些 api 可安全重播。 |
| Guid 和 Uuid  | 返回随机 GUID 或 UUID 的 Api 是不确定性的，因为每次重播生成的值不同。 | 在 .NET 中使用[**NewGuid**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid)或在 JavaScript 中使用**NewGuid**来安全地生成随机 guid。 |
| 随机数字 | 返回随机数的 Api 是不确定性的，因为每次重播生成的值不同。 | 使用活动函数可将随机数值返回给业务流程。 活动函数的返回值对于重播始终是安全的。 |
| 绑定 | 输入和输出绑定通常执行 i/o 操作，并且不具有确定性。 业务流程协调程序函数不能直接使用甚至是[业务流程客户端](durable-functions-bindings.md#orchestration-client)[绑定。](durable-functions-bindings.md#entity-client) | 在客户端或活动函数内使用输入绑定和输出绑定。 |
| 网络 | 网络调用涉及外部系统并且具有不确定性。 | 使用活动函数进行网络调用。 如果需要从业务流程协调程序函数发出 HTTP 调用，还可以使用[持久 HTTP api](durable-functions-http-features.md#consuming-http-apis)。 |
| 阻塞 Api | 阻塞 Api，如 **Thread。在 .NET 中，睡眠 @ no__t，类似的 Api 可能会导致 orchestrator 函数的性能和规模出现问题，应避免此问题。 在 Azure Functions 消耗计划中，它们甚至会导致不必要的运行时费用。 | 当 Api 可用时，使用替代项来阻止 Api。 例如，使用**CreateTimer**引入业务流程执行中的延迟。 [持久计时器](durable-functions-timers.md)延迟不计入业务流程协调程序函数的执行时间。 |
| 异步 Api | 除了使用[**DurableOrchestrationContext**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API 或**CONTEXT.SUBNAME**对象的 api 以外，Orchestrator 代码决不能启动任何异步操作。 例如，不能在 JavaScript**中使用**HttpClient、 **SendAsync**或 setTimeout 和 setInterval 中的和 。 持久任务框架在单个线程上运行 orchestrator 代码。 它不能与其他异步 Api 调用的任何其他线程交互。 | 业务流程协调程序函数只应进行持久的异步调用。 活动函数应进行任何其他异步 API 调用。 |
| 异步 JavaScript 函数 | 不能将 JavaScript orchestrator 函数声明为**async** ，因为 node.js 运行时不保证异步函数是确定性的。 | 将 JavaScript orchestrator 函数声明为同步生成器函数。 |
| 线程 Api | 持久任务框架在单个线程上运行 orchestrator 代码，不能与任何其他线程交互。 向业务流程的执行引入新线程可能会导致不确定的执行或死锁。 | 业务流程协调程序函数应该几乎不会使用线程 Api。 如果需要此类 Api，请将其使用限制为仅活动函数。 |
| 静态变量 | 避免在 orchestrator 函数中使用非常量静态变量，因为它们的值可能会随时间而变化，从而导致不确定的运行时行为。 | 使用常量，或将静态变量限制为活动函数。 |
| 环境变量 | 请勿在 orchestrator 函数中使用环境变量。 它们的值可能会随时间而变化，从而导致不确定的运行时行为。 | 环境变量只能在客户端函数或活动函数内引用。 |
| 无限循环 | 请避免在业务流程协调程序函数中出现无限循环。 由于持久任务框架会在业务流程函数进行过程中保存执行历史记录，因此无限循环可能会导致 orchestrator 实例耗尽内存。 | 对于无限循环方案，请使用 .NET 中的[**ContinueAsNew**](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_)或 JavaScript 中的**ContinueAsNew**来重新启动函数执行，并放弃以前的执行历史记录。 |

尽管应用这些约束的方法最初可能会很困难，但实际上它们是很容易的。

持久任务框架尝试检测前面规则的冲突。 如果发现冲突，框架将引发**NonDeterministicOrchestrationException**异常。 但是，此检测行为不会捕获所有冲突，因此不应依赖于它。

## <a name="versioning"></a>版本控制

持久的业务流程可能会持续运行数天、月、年甚至[永久](durable-functions-eternal-orchestrations.md)。 对影响未完成业务流程的 Durable Functions 应用进行的任何代码更新都可能会破坏业务流程的重播行为。 这就是为什么在更新代码时进行仔细规划非常重要的原因。 有关如何对代码进行版本管理的更详细说明，请参阅[版本管理一文](durable-functions-versioning.md)。

## <a name="durable-tasks"></a>持久任务

> [!NOTE]
> 本部分介绍 Durable Task Framework 的内部实现详细信息。 无需知道此信息，即可使用持久性函数。 本部分旨在帮助读者了解重播行为。

可在业务流程协调程序函数中安全等待的任务有时称为*持久任务*。 持久任务框架创建和管理这些任务。 例如， **CallActivityAsync**、 **WaitForExternalEvent**和**CreateTimer**在 .net orchestrator 函数中返回的任务。

这些持久任务由 .NET 中的**TaskCompletionSource**对象列表在内部进行管理。 在重播过程中，将在执行 orchestrator 代码的过程中创建这些任务。 完成后，调度程序会枚举相应的历史记录事件。

使用单个线程以同步方式执行任务，直到重播所有历史记录。 在历史记录重播结束时未完成的持久任务会执行相应的操作。例如，消息可能会排队以调用活动函数。

本部分对运行时行为的说明应有助于了解协调器函数无法在非持久任务中使用**await**或**yield**的原因。 有两个原因：调度程序线程无法等待任务完成，并且该任务的任何回调都可能会损坏 orchestrator 函数的跟踪状态。 某些运行时检查已准备就绪，可帮助检测这些冲突。

若要了解有关持久任务框架如何执行业务流程协调程序函数的详细信息，请参阅[GitHub 上的持久任务源代码](https://github.com/Azure/durabletask)。 具体而言，请参阅[TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs)和[TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何调用子业务流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)
