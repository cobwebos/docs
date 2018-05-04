---
title: 处理 Durable Functions 中的错误 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理错误。
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 108c6020956b398effb8ba9dd4471190362359d6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>处理 Durable Functions 中的错误 (Azure Functions)

Durable Function 业务流程采用代码实现，并可使用编程语言的错误处理功能。 考虑到这一点，将错误处理和补偿合并到业务流程时，无需了解任何新概念。 但应注意以下事项。

## <a name="errors-in-activity-functions"></a>活动函数中的错误

活动函数中引发的任何异常都将封送回业务流程协调程序函数，并作为 `FunctionFailedException` 引发。 可在业务流程协调程序函数中编写满足需要的错误处理和补偿代码。

例如，考虑使用以下业务流程协调程序函数，将一个帐户中的资金转到另一帐户：

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
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

如果目标帐户的 CreditAccount 函数调用失败，则业务流程协调程序函数通过将资金归还源帐户来进行补偿。

## <a name="automatic-retry-on-failure"></a>失败时自动重试

调用活动函数或子业务流程函数时，可指定自动重试策略。 以下示例试图调用某函数多达 3 次，且每次重试之间等待时间 5 秒：

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);
    
    // ...
}
```

`CallActivityWithRetryAsync` API 采用 `RetryOptions` 参数。 使用 `CallSubOrchestratorWithRetryAsync` API 的子业务流程调用均可使用这些相同的重试策略。

可通过多种选项自定义自动重试策略。 包含以下内容：

* **最大尝试次数**：重试的最大次数。
* **首次重试间隔**：首次重试前需要等待的时间。
* **回退系数**：用来确定回退增加速率的系数。 默认值为 1。
* **最大重试间隔**：每次重试之间需要等待的最大时间。
* **重试超时**：执行重试的最大时间。 默认行为是可无限期重试。
* **自定义**：可指定用户定义的回退，确定是否应该重试函数调用。

## <a name="function-timeouts"></a>函数超时

如果业务流程协调程序内的函数调用耗时太长，建议放弃该函数调用。 本示例中执行此操作的正确方法是将 `context.CreateTimer` 和 `Task.WhenAny` 结合使用，创建[持久计时器](durable-functions-timers.md)，如下例中所示：

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
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

## <a name="unhandled-exceptions"></a>未经处理的异常

如果业务流程协调程序函数失败，出现未经处理的异常，则会记录异常的详细信息，且实例的完成状态为 `Failed`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何诊断问题](durable-functions-diagnostics.md)
