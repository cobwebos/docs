---
title: 持久性业务流程协调程序代码约束 - Azure Functions
description: 适用于 Azure Durable Functions 的业务流程函数重播和代码约束。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 5013457aca99a63808077b86f5674460e83fdc41
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232981"
---
# <a name="orchestrator-function-code-constraints"></a>业务流程协调程序函数代码约束

Durable Functions 是 [Azure Functions](../functions-overview.md) 的一个扩展，用于构建有状态应用。 可以使用[业务流程协调程序函数](durable-functions-orchestrations.md)来协调函数应用中其他持久函数的执行。 业务流程协调程序函数带有状态且可靠，可以长时间运行。

## <a name="orchestrator-code-constraints"></a>业务流程协调程序代码约束

业务流程协调程序函数使用[事件源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)来确保可靠执行并保持本地变量状态。 业务流程协调程序代码的[重播行为](durable-functions-orchestrations.md#reliability)针对可在业务流程协调程序函数中编写的代码类型创建约束。 例如，业务流程协调程序函数必须具有确定性：业务流程协调程序函数将重播多次，每次必须生成相同的结果。

### <a name="using-deterministic-apis"></a>使用确定性 API

本部分提供一些简单的指导原则，用于帮助确保代码是确定性的。

业务流程协调程序函数可以采用目标语言调用任何 API。 但是，必须确保业务流程协调程序函数只调用确定性 API。 确定性 API 是一种在输入相同时始终返回同一值的 API，不管何时调用，也不管调用频率如何。

下表显示了应该避免使用的 API 的一些示例，因为它们不是确定性的。 这些限制仅适用于业务流程协调程序函数。 其他函数类型没有此类限制。

| API 类别 | Reason | 解决方法 |
| ------------ | ------ | ---------- |
| 日期和时间  | 返回当前日期或时间的 API 是非确定性的，因为每次重播时它们返回的值都不相同。 | 在 .NET 中使用`CurrentUtcDateTime` API，或使用 JavaScript 中可安全重播的 `currentUtcDateTime` API。 |
| GUID 和 UUID  | 返回随机 GUID 或 UUID 的 API 是非确定性的，因为每次重播时它们生成的值都不相同。 | 使用 .NET 中的 `NewGuid` 或 JavaScript 中的 `newGuid` 来安全地生成随机 Guid。 |
| 随机数 | 返回随机数的 API 是非确定性的，因为每次重播时它们生成的值都不相同。 | 使用活动函数将随机数返回给业务流程。 就重播来说，活动函数的返回值始终是安全的。 |
| 绑定 | 输入和输出绑定通常会执行 I/O 操作，是非确定性的。 即使是[业务流程客户端](durable-functions-bindings.md#orchestration-client)和[实体客户端](durable-functions-bindings.md#entity-client)绑定，也不得由业务流程协调程序函数直接使用。 | 在客户端或活动函数中使用输入和输出绑定。 |
| 网络 | 网络调用涉及外部系统，是非确定性的。 | 使用活动函数进行网络调用。 如果需要从业务流程协调程序函数进行 HTTP 调用，则也可使用[持久性 HTTP API](durable-functions-http-features.md#consuming-http-apis)。 |
| 阻止 API | 阻止 Api （如 .NET 中的 `Thread.Sleep` 和类似的 Api）可能会导致 orchestrator 函数的性能和规模出现问题，应避免这样做。 在 Azure Functions 消耗计划中，它们甚至可能导致不必要的运行时收费。 | 在适用的情况下使用阻止 API 的替代方案。 例如，使用 `CreateTimer` 在业务流程执行中引入延迟。 [持久计时器](durable-functions-timers.md)延迟不计入业务流程协调程序函数的执行时间。 |
| 异步 API | 除了使用 `IDurableOrchestrationContext` API 或 `context.df` 对象的 API 以外，Orchestrator 代码决不能启动任何异步操作。 例如，不能在 .NET 中使用 `Task.Run`、`Task.Delay`和 `HttpClient.SendAsync`，也不能在 JavaScript 中使用 `setTimeout` 和 `setInterval`。 Durable Task Framework 在单个线程上执行业务流程协调程序代码。 它不能与可由其他异步 API 调用的其他任何线程交互。 | 业务流程协调程序函数只应进行持久性异步调用。 应该由活动函数进行任何其他异步 API 调用。 |
| 异步 JavaScript 函数 | 不能将 JavaScript orchestrator 函数声明为 `async`，因为 node.js 运行时不保证异步函数是确定性的。 | 将 JavaScript 业务流程协调程序函数声明为同步的生成器函数。 |
| 线程 API | Durable Task Framework 在单个线程上运行业务流程协调程序代码，不能与任何其他线程交互。 将新线程引入业务流程的执行中可能导致非确定性执行或死锁。 | 在绝大多数情况下，业务流程协调程序函数不应使用线程 API。 如果需要使用此类 API，请将其使用限制为活动函数。 |
| 静态变量 | 避免在业务流程协调程序函数中使用非常量静态变量，因为其值可能随时间而变，导致非确定性的运行时行为。 | 使用常量，或者将静态变量的使用限制为活动函数。 |
| 环境变量 | 请勿在业务流程协调程序函数中使用环境变量。 其值可能随时间而变，导致非确定性的运行时行为。 | 环境变量只能在客户端函数或活动函数内部引用。 |
| 无限循环 | 请避免在业务流程协调程序函数中出现无限循环。 由于 Durable Task Framework 在业务流程函数的执行过程中会保存执行历史记录，无限循环可能会导致业务流程协调程序实例耗尽内存。 | 对于无限循环方案，请使用 .NET 中的 `ContinueAsNew` Api 或 JavaScript 中的 `continueAsNew` 来重新启动函数执行，并放弃以前的执行历史记录。 |

尽管这些约束看起来很难应用，但其实并不难遵守。

Durable Task Framework 会尝试检测上述规则的违规。 如果发现违规，该框架将引发 **NonDeterministicOrchestrationException** 异常。 但是，此检测行为不会捕获所有违规，因此请不要对它有依赖。

## <a name="versioning"></a>版本控制

持久性业务流程可能会连续运行数天、数月、数年，甚至会[永久](durable-functions-eternal-orchestrations.md)执行。 对 Durable Functions 应用进行代码更新时，如果此类更新影响尚未完成的业务流程，则可能会中断业务流程的重播行为。 因此，在更新代码时请仔细进行计划，这很重要。 有关如何进行代码版本控制的更详细说明，请参阅[版本控制](durable-functions-versioning.md)一文。

## <a name="durable-tasks"></a>持久任务

> [!NOTE]
> 本部分介绍 Durable Task Framework 的内部实现详细信息。 在不了解这些信息的情况下也可以使用 Durable Functions。 本部分旨在帮助读者了解重播行为。

可在业务流程协调程序函数中安全等待的任务有时称为“持久任务”。 这些任务由 Durable Task Framework 创建和管理。 示例包括 .NET 业务流程协调程序函数中的 **CallActivityAsync**、**WaitForExternalEvent** 和 **CreateTimer** 返回的任务。

这些持久任务由 .NET 中的 `TaskCompletionSource` 对象列表在内部进行管理。 在重播期间，这些任务在业务流程协调程序代码执行过程中予以创建。 在调度程序枚举相应历史记录事件时，这些任务将会完成。

这些任务通过单个线程以同步方式执行，直至重播完所有历史记录。 在历史记录重播结束时未完成的持久任务会执行相应的操作。例如，消息可能会排队以调用活动函数。

本部分对运行时行为的说明应有助于了解协调器函数无法在非持久任务中使用 `await` 或 `yield` 的原因。 有两个原因：调度程序线程无法等待该任务完成，并且该任务发出的任何回调可能会损坏业务流程协调程序函数的跟踪状态。 进行某些运行时检查是为了帮助检测此类违规。

若要详细了解 Durable Task Framework 如何执行业务流程协调程序函数，请查阅 [GitHub 上的持久任务源代码](https://github.com/Azure/durabletask)。 具体而言，请参阅 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 和 [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何调用子业务流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)
