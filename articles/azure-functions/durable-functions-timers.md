---
title: Durable Functions 中的计时器 - Azure
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的持久计时器。
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 4fd86b70965a7be84c72e265af798292819cbe96
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions 中的计时器 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 提供了供在业务流程协调程序函数中使用的“持久计时器”，这些计时器用来为异步操作实现延迟或设置超时。 在业务流程协调程序函数中应当使用持久计时器，而不是使用 `Thread.Sleep` 和 `Task.Delay` (C#) 或 `setTimeout()` 和 `setInterval()` (JavaScript)。

通过在 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) 中调用 [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) 方法创建持久计时器。 该方法返回一个将在指定的日期和时间恢复运行的任务。

## <a name="timer-limitations"></a>计时器限制

创建在下午 4:30 过期的计时器时，基础 Durable Task Framework 会将一条仅在下午 4:30 才变得可见的消息排入队列。 当在 Azure Functions 消耗计划中运行时，新近可见的计时器消息将确保在合适的 VM 上激活函数应用。

> [!NOTE]
> * 由于 Azure 存储中的限制，持久计时器的持续时间不能超过 7 天。 我们正在致力于解决[将计时器扩展到 7 天以上的功能请求](https://github.com/Azure/azure-functions-durable-extension/issues/14)。
> * 在计算持久计时器的相对截止时间时，请始终使用 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) 而非 `DateTime.UtcNow`，如以下示例中所示。

## <a name="usage-for-delay"></a>延迟的用法

下面的示例演示了如何使用持久计时器来延迟执行。 示例连续十天每天都会发出账单通知。

#### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivityAsync("SendBillingEvent");
    }
});
```

> [!WARNING]
> 请避免在业务流程协调程序函数中出现无限循环。 有关如何安全有效地实现无限循环方案的信息，请参阅[永久业务流程](durable-functions-eternal-orchestrations.md)。 

## <a name="usage-for-timeout"></a>超时的用法

此示例演示了如何使用持久计时器来实现超时。

#### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, 's');

    const activityTask = context.df.callActivityAsync("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

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

> [!WARNING]
> 使用 `CancellationTokenSource` 取消持久计时器 (C#) 或对返回的 `TimerTask` (JavaScript) 调用 `cancel()`（如果你的代码不会等待它完成）。 在所有未完成任务都完成或取消之前，Durable Task Framework 不会将业务流程的状态更改为“已完成”。

此机制实际上不会终止正在进行的活动函数执行。 它只是允许业务流程协调程序函数忽略结果并继续运行。 如果函数应用使用了消耗计划，则还需要为已放弃的活动函数消耗的任何时间和内存付费。 默认情况下，在消耗计划中运行的函数有五分钟的超时。 如果超出了此限制，则会回收 Azure Functions 主机以停止所有执行并防止出现费用失控的情况。 [函数超时是可配置的](functions-host-json.md#functiontimeout)。

有关如何在业务流程协调程序函数中实现超时的更深入示例，请参阅[人机交互和超时 - 电话验证](durable-functions-phone-verification.md)演练。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何引发和处理外部事件](durable-functions-external-events.md)

