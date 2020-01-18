---
title: 处理 Durable Functions 中的错误 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理错误。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 905b71ab1e9a054eaeb6087489d14565933c8a46
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261630"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>处理 Durable Functions 中的错误 (Azure Functions)

持久函数业务流程是在代码中实现的，并且可以使用编程语言的内置错误处理功能。 无需了解将错误处理和补偿添加到业务流程中所需的任何新概念。 但应注意以下事项。

## <a name="errors-in-activity-functions"></a>活动函数中的错误

在活动函数中引发的任何异常都将封送回 orchestrator 函数并作为 `FunctionFailedException`引发。 可在业务流程协调程序函数中编写满足需要的错误处理和补偿代码。

例如，考虑使用以下业务流程协调程序函数，将一个帐户中的资金转移到另一帐户：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> 前面C#的示例适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

如果第一个**CreditAccount**函数调用失败，则业务流程协调程序函数通过将资金贷记回源帐户来进行补偿。

## <a name="automatic-retry-on-failure"></a>失败时自动重试

调用活动函数或子业务流程函数时，可指定自动重试策略。 以下示例尝试调用某个函数多达 3 次，且每次重试之间等待 5 秒：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> 前面C#的示例适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

上一示例中的活动函数调用使用参数来配置自动重试策略。 自定义自动重试策略有几个选项：

* **最大尝试次数**：重试的最大次数。
* **首次重试间隔**：首次重试前需要等待的时间。
* **回退系数**：用来确定回退增加速率的系数。 默认值为 1。
* **最大重试间隔**：每次重试之间需要等待的最大时间。
* **重试超时**：执行重试的最大时间。 默认行为是可无限期重试。
* **句柄**：可以指定用户定义的回调来确定是否应重试某个函数。

## <a name="function-timeouts"></a>函数超时

如果在业务流程协调程序函数中完成的时间太长，可能需要放弃该函数调用。 本示例中执行此操作的正确方法是将 `context.CreateTimer` (.NET) 或 `context.df.createTimer` (JavaScript) 与 `Task.WhenAny` (.NET) 或 `context.df.Task.any` (JavaScript) 结合使用，创建[持久计时器](durable-functions-timers.md)，如下例中所示：

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> 前面C#的示例适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> 此机制实际上不会终止正在进行的活动函数执行。 它只是允许业务流程协调程序函数忽略结果并继续运行。 有关详细信息，请参阅[计时器](durable-functions-timers.md#usage-for-timeout)文档。

## <a name="unhandled-exceptions"></a>未经处理的异常

如果业务流程协调程序函数失败，出现未经处理的异常，则会记录异常的详细信息，且实例的完成状态为 `Failed`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解永久业务流程](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何诊断问题](durable-functions-diagnostics.md)
