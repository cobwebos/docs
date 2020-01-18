---
title: Durable Functions 中的计时器 - Azure
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的持久计时器。
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261477"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions 中的计时器 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 提供了供在业务流程协调程序函数中使用的“持久计时器”，这些计时器用来为异步操作实现延迟或设置超时。 在业务流程协调程序函数中应当使用持久计时器，而不是使用 `Thread.Sleep` 和 `Task.Delay` (C#) 或 `setTimeout()` 和 `setInterval()` (JavaScript)。

可以通过调用 `CreateTimer` （.NET）方法或[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)的 `createTimer` （JavaScript）方法来创建持久计时器。 方法返回在指定的日期和时间完成的任务。

## <a name="timer-limitations"></a>计时器限制

当你创建一个在下午4:30 过期的计时器时，底层持久任务框架将排队一条仅在 4:30 pm 变为可见的消息。 在 Azure Functions 消耗计划中运行时，新可见的计时器消息将确保在适当的 VM 上激活函数应用。

> [!NOTE]
> * 持久计时器目前限制为7天。 如果需要更长的延迟，可以使用 `while` 循环中的计时器 Api 来模拟。
> * 在计算持久计时器的触发时间时，请始终使用 `CurrentUtcDateTime` 而不是在 .NET 或 `currentUtcDateTime` 中 `DateTime.UtcNow`，而不是在 JavaScript 中使用 `Date.now` 或 `Date.UTC`。 有关详细信息，请参阅业务流程[协调程序函数代码约束](durable-functions-code-constraints.md)一文。

## <a name="usage-for-delay"></a>延迟的用法

下面的示例演示了如何使用持久计时器来延迟执行。 该示例每天发布一次帐单通知10天。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> 上面C#的示例针对 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> 请避免在业务流程协调程序函数中出现无限循环。 有关如何安全有效地实现无限循环方案的信息，请参阅[永久业务流程](durable-functions-eternal-orchestrations.md)。

## <a name="usage-for-timeout"></a>超时的用法

此示例演示了如何使用持久计时器来实现超时。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 上面C#的示例针对 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

---

> [!WARNING]
> 如果代码不等待完成，请使用 `CancellationTokenSource` （.NET）或在返回的 `TimerTask` （JavaScript）上调用 `cancel()` 以取消持久计时器。 在完成或取消所有未完成的任务之前，持久性任务框架不会将业务流程的状态更改为 "已完成"。

此取消机制不会终止正在进行的活动函数或子业务流程的执行。 它只是允许业务流程协调程序函数忽略结果并继续运行。 如果函数应用使用消耗计划，则仍将对被放弃的活动函数使用的任何时间和内存进行计费。 默认情况下，在消耗计划中运行的函数有五分钟的超时。 如果超出了此限制，则会回收 Azure Functions 主机以停止所有执行并防止出现费用失控的情况。 [函数超时是可配置的](../functions-host-json.md#functiontimeout)。

有关如何在业务流程协调程序函数中实现超时的更深入示例，请参阅用户[交互 & 超时-电话验证一](durable-functions-phone-verification.md)文。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何引发和处理外部事件](durable-functions-external-events.md)
