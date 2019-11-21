---
title: 处理 Durable Functions 中的错误 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理错误。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0900e3f3b76f4a82e06fe3c0e6d9bbe63b545f56
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231408"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>处理 Durable Functions 中的错误 (Azure Functions)

Durable Function orchestrations are implemented in code and can use the programming language's built-in error-handling features. There really aren't any new concepts you need to learn to add error handling and compensation into your orchestrations. 但应注意以下事项。

## <a name="errors-in-activity-functions"></a>活动函数中的错误

Any exception that is thrown in an activity function is marshaled back to the orchestrator function and thrown as a `FunctionFailedException`. 可在业务流程协调程序函数中编写满足需要的错误处理和补偿代码。

例如，考虑使用以下业务流程协调程序函数，将一个帐户中的资金转移到另一帐户：

### <a name="precompiled-c"></a>预编译 C#

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

### <a name="c-script"></a>C# 脚本

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(IDurableOrchestrationContext context)
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
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

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

If the first **CreditAccount** function call fails, the orchestrator function compensates by crediting the funds back to the source account.

## <a name="automatic-retry-on-failure"></a>失败时自动重试

调用活动函数或子业务流程函数时，可指定自动重试策略。 以下示例尝试调用某个函数多达 3 次，且每次重试之间等待 5 秒：

### <a name="precompiled-c"></a>预编译 C#

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

### <a name="c-script"></a>C# 脚本

```csharp
public static async Task Run(IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync` (.NET) 或 `callActivityWithRetry` (JavaScript) API 带有 `RetryOptions` 参数。 Sub-orchestration calls using the `CallSubOrchestratorWithRetryAsync` (.NET) or `callSubOrchestratorWithRetry` (JavaScript) API can use these same retry policies.

There are several options for customizing the automatic retry policy:

* **最大尝试次数**：重试的最大次数。
* **首次重试间隔**：首次重试前需要等待的时间。
* **回退系数**：用来确定回退增加速率的系数。 默认值为 1。
* **最大重试间隔**：每次重试之间需要等待的最大时间。
* **重试超时**：执行重试的最大时间。 默认行为是可无限期重试。
* **Handle**: A user-defined callback can be specified to determine whether a function should be retried.

## <a name="function-timeouts"></a>函数超时

You might want to abandon a function call within an orchestrator function if it's taking too long to complete. 本示例中执行此操作的正确方法是将 `context.CreateTimer` (.NET) 或 `context.df.createTimer` (JavaScript) 与 `Task.WhenAny` (.NET) 或 `context.df.Task.any` (JavaScript) 结合使用，创建[持久计时器](durable-functions-timers.md)，如下例中所示：

### <a name="precompiled-c"></a>预编译 C#

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

### <a name="c-script"></a>C# 脚本

```csharp
public static async Task<bool> Run(IDurableOrchestrationContext context)
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
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

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

> [!NOTE]
> 此机制实际上不会终止正在进行的活动函数执行。 它只是允许业务流程协调程序函数忽略结果并继续运行。 有关详细信息，请参阅[计时器](durable-functions-timers.md#usage-for-timeout)文档。

## <a name="unhandled-exceptions"></a>未经处理的异常

如果业务流程协调程序函数失败，出现未经处理的异常，则会记录异常的详细信息，且实例的完成状态为 `Failed`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Learn about eternal orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何诊断问题](durable-functions-diagnostics.md)
