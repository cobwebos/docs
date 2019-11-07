---
title: Durable Functions 中的永久业务流程 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 扩展实现永久业务流程。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 99f57f2e0b34f2e596ff9cf1a872650228ef0acd
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614853"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions 中的永久业务流程 (Azure Functions)

*永久业务流程*是永远不会结束的业务流程协调程序函数。 当希望将 [Durable Functions](durable-functions-overview.md) 用于聚合器和需要无限循环的任何方案时，永久业务流程非常有用。

## <a name="orchestration-history"></a>业务流程历史记录

如[业务流程历史记录](durable-functions-orchestrations.md#orchestration-history)主题中所述，Durable Task Framework 会跟踪每个函数业务流程的历史记录。 只要业务流程协调程序函数继续计划新工作，此历史记录就会不断增长。 如果业务流程协调程序函数进入无限循环并持续计划工作，则此历史记录可能会变得非常大并导致明显的性能问题。 *永久业务流程*概念是为了减轻需要无限循环的应用程序的此类问题而设计的。

## <a name="resetting-and-restarting"></a>重置和重启

业务流程协调程序函数通过调用[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)的 `ContinueAsNew` （.net）或 `continueAsNew` （JavaScript）方法来重置其状态，而不是使用无限循环。 此方法采用单个 JSON 可序列化参数，该参数将成为用于生成下一个业务流程协调程序函数的新输入。

当调用 `ContinueAsNew` 时，实例会在其退出之前将一条消息排入其自己的队列。 该消息将使用新的输入值重启实例。 将保持同一实例 ID，但业务流程协调程序函数的历史记录实际上会被截断。

> [!NOTE]
> Durable Task Framework 会维护同一个实例 ID，但在内部会为由  *重置的业务流程协调程序函数创建一个新的“执行 ID”。* `ContinueAsNew` 此执行 ID 通常不对外公开，但在调试业务流程执行时知道该 ID 可能比较有用。

## <a name="periodic-work-example"></a>定期工作示例

永久业务流程的一个用例是需要无限期执行定期工作的代码。

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> 上面C#的示例适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

### <a name="javascript-functions-20-only"></a>JavaScript （仅限函数2.0）

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

此示例与计时器触发的函数之间的区别是此处的清理触发时间不基于计划。 例如，每小时执行某个函数的 CRON 计划将在 1:00、2:00 和 3:00 等时间执行，并且可能会遇到重叠问题。 不过，在此示例中，如果清理花费 30 分钟，则它将计划在 1:00、2:30、4:00 等时间执行，因此不可能重叠。

## <a name="starting-an-eternal-orchestration"></a>启动永久业务流程

使用 `StartNewAsync` （.NET）或 `startNew` （JavaScript）方法来启动永久业务流程，就像对任何其他业务流程功能一样。  

> [!NOTE]
> 如果需要确保单一永久业务流程实例正在运行，则在启动业务流程时维护相同的实例 `id` 非常重要。 有关详细信息，请参阅[实例管理](durable-functions-instance-management.md)。

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> 前面的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

## <a name="exit-from-an-eternal-orchestration"></a>从永久业务流程退出

如果业务流程协调程序函数需要最终完成，则你需要做的全部工作“不是”调用  *而是让函数退出。* `ContinueAsNew`

如果业务流程协调程序函数处于无限循环并且需要停止，请使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `TerminateAsync` （.net）或 `terminate` （JavaScript）方法将其停止。 有关详细信息，请参阅[实例管理](durable-functions-instance-management.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何实现单一实例业务流程](durable-functions-singletons.md)
