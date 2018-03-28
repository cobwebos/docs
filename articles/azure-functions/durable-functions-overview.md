---
title: Durable Functions 概述 - Azure（预览版）
description: Azure Functions 的 Durable Functions 扩展简介。
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
ms.openlocfilehash: b5269bb51c787c927b4224b3520d5514b6d24501
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="durable-functions-overview-preview"></a>Durable Functions 概述（预览版）

Durable Functions 是 [Azure Functions](functions-overview.md) 和 [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) 的扩展，可用于在无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。

使用此扩展，可以在名为业务流程协调程序函数的新型函数中定义有状态工作流。 业务流程协调程序函数的优点包括：

* 可在代码中定义工作流。 无需 JSON 架构或设计器。
* 可同步和异步调用其他函数。 调用函数的输出可保存到本地变量。
* 每当有函数处于等待状态时，可自动对进度执行检查点操作。 回收进程或重启 VM 时，从来不会丢失本地状态。

> [!NOTE]
> Durable Functions 处于预览状态，是 Azure Functions 的高级扩展，并非适用于所有应用程序。 本文其余部分假设用户已非常熟悉 [Azure Functions](functions-overview.md) 概念，以及在无服务器应用程序开发过程中面临的挑战。

Durable Functions 的主要用例是简化无服务器应用程序中出现的复杂的有状态协调问题。 以下各节介绍可受益于 Durable Functions 的部分典型应用程序模式。

## <a name="pattern-1-function-chaining"></a>模式 1：函数链

函数链是指以特定顺序执行一系列函数的模式。 通常需要将一个函数的输出应用于另一函数的输入。

![函数链关系图](media/durable-functions-overview/function-chaining.png)

借助 Durable Functions，可在代码中简明地实现此模式。

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

值“F1”、“F2”、“F3”和“F4”是函数应用中其他函数的名称。 控制流通过使用常规命令性编码构造实现。 即代码从上至下执行，并且可能涉及现有语言控制流语义，如条件语句和循环语句。  try/catch/finally 块中可包含错误处理逻辑。

`ctx` 参数 ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) 提供按名称、传递参数和返回函数输出调用其他函数的方法。 每当代码调用 `await` 时，Durable Functions 框架都会对当前函数实例的进度执行检查点操作。 如果在执行中途回收进程或 VM，函数实例从上一个 `await` 调用恢复。 后文详细介绍了这一重启行为。

## <a name="pattern-2-fan-outfan-in"></a>模式 2：扇出/扇入

扇出/扇入是指并行执行多个函数，并等待所有执行完成的模式。  通常会对这些函数返回的结果执行一些聚合操作。

![扇出/扇入关系图](media/durable-functions-overview/fan-out-fan-in.png)

对于普通函数，可通过使函数向一个队列发送多条消息来完成扇出。 但是，扇入回来更具挑战性。 需要编写代码，跟踪队列触发的函数何时结束，并存储函数输出。 Durable Functions 扩展可使用相对简单的代码处理这种模式。

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

扇出操作会分发到函数 `F2` 的多个实例中，并且可通过使用动态任务列表跟踪这些操作。 将调用 .NET `Task.WhenAll` API，等待所有调用的函数完成。 然后，从动态任务列表聚合 `F2` 函数输出，并将这些输出传递给 `F3` 函数。

在对 `Task.WhenAll` 进行 `await` 调用处自动执行的检查点操作可确保中途出现的任何崩溃或重启均无需重启已完成的任务。

## <a name="pattern-3-async-http-apis"></a>模式 3：异步 HTTP API

第三种模式全部关于使用外部客户端协调长时间运行的操作的状态时出现的问题。 实现此模式的常用方式是由 HTTP 调用触发长时间运行的操作，然后将客户端重定向到状态终结点，可对该终结点进行轮询，以了解操作完成的时间。

![HTTP API 图](media/durable-functions-overview/async-http-api.png)

Durable Functions 提供内置 API，可简化为与长时间运行的函数执行进行交互而编写的代码。 [示例](durable-functions-install.md)演示了可用于启动新业务流程协调程序函数实例的简单 REST 命令。 启动实例后，扩展会公开 webhook HTTP API，查询业务流程协调程序函数状态。 以下示例演示用于启动业务流程协调程序和查询其状态的 REST 命令。 为清楚起见，实例中省略了某些细节。

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

因为状态由 Durable Functions 运行时托管，因此无需实施自己的状态跟踪机制。

即使 Durable Functions 扩展拥有内置的 webhook 可用于管理长时间运行的业务流程，仍可使用自己的函数触发器（如 HTTP、队列或事件中心）和 `orchestrationClient` 绑定来自行实现此模式。 例如，可以使用队列消息触发终止。  或者，可以使用受 Azure Active Directory 身份验证策略保护的 HTTP 触发器，而不是使用利用生成的密钥进行身份验证的内置 webhook。 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` 参数是 `orchestrationClient` 输出绑定中的值，该绑定是 Durable Functions 扩展的一部分。 它提供用于启用、终止和查询新的或现有业务流程协调程序函数实例以及向这些实例发送事件的方法。 在上面的示例中，由 HTTP 触发的函数采用传入的 URL 中的 `functionName` 值，并将该值传递给 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)。 然后，此绑定 API 返回包含 `Location` 标头和有关实例的其他信息的响应，这些信息稍后可用于查找已启动实例的状态或终止实例。

## <a name="pattern-4-monitoring"></a>模式 #4：监视

监视模式是指工作流中灵活的重复过程 - 例如，反复轮询，直到满足特定的条件为止。 常规计时器触发器可以解决简单方案（如定期清理作业），但其时间间隔是静态的，管理实例生存期会变得复杂。 Durable Functions 可实现灵活的重复间隔、任务生存期管理，并可以从单个业务流程创建多个监视器进程。

下面的示例会完全改变前面的异步 HTTP API 方案。 不是公开终结点供外部客户端监视长时间运行的操作，而是让长时间运行的监视器使用外部终结点，等待某个状态更改。

![监视器关系图](media/durable-functions-overview/monitor.png)

使用 Durable Functions，可以通过几行代码创建观察任意终结点的多个监视器。 当某个条件满足时，监视器可以结束执行或由 [DurableOrchestrationClient](durable-functions-instance-management.md) 终止，可以基于某个条件更改其等待间隔（即指数回退）。下面的代码实现了基本的监视器。

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

收到请求时，会为该作业 ID 创建新的业务流程实例。 该实例会一直轮询状态，直到满足条件退出循环。 持久计时器用于控制轮询间隔。 然后可以执行进一步的工作，也可以结束业务流程。 当 `ctx.CurrentUtcDateTime` 超过 `expiryTime` 时，监视结束。

## <a name="pattern-5-human-interaction"></a>模式 5：人机交互

许多过程均涉及某种类型的人机交互。 在自动化过程中，人机交互让人很棘手的就是人的可用性和响应性并不总是与云服务一样高。 自动化过程必须考虑到这一点，这通常可通过使用超时和补偿逻辑来实现。

审批过程是一个涉及人机交互的业务过程示例。 例如，超出特定金额的费用报销单可能需要经理进行审批。 如果经理未在 72 小时内审核（可能正在度假），则上报过程启动，以便其他人（可能是经理的经理）进行审批。

![人机交互图](media/durable-functions-overview/approval.png)

可使用业务流程协调程序函数实现此模式。 业务流程协调程序使用[持久计时器](durable-functions-timers.md)来请求审批，并在发生超时的情况下进行上报。 该程序等待一个[外部事件](durable-functions-external-events.md)，该事件为某个人机交互生成的通知。

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

持久计时器通过调用 `ctx.CreateTimer` 创建。 通知由 `ctx.WaitForExternalEvent` 接收。 还将调用 `Task.WhenAny`，确定是上报（首先发生超时）还是处理审批（超时前收到审批）。

## <a name="the-technology"></a>技术

在后台，Durable Functions 扩展在 [Durable Task Framework](https://github.com/Azure/durabletask) 的基础上生成，后者是 GitHub 上用于生成持久任务业务流程的开源库。 与 Azure Functions 是 Azure WebJobs 的无服务器演进非常相似，Durable Functions 也是 Durable Task Framework 的无服务器演进。 Durable Task Framework 大量用于 Microsoft 内外，以及自动处理任务关键型过程。 它天生就很适合无服务器 Azure Functions 环境。

### <a name="event-sourcing-checkpointing-and-replay"></a>事件溯源、检查点和重播

业务流程协调程序函数使用云设计模式（称为[事件溯源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)）可靠地维护其执行状态。 持久扩展使用仅限追加的存储来记录函数业务流程执行的一系列完整操作，而非直接存储业务流程的当前状态。 与“转储”完整的运行时状态相比，这具有诸多优点，包括提升性能、可伸缩性和响应能力。 其他优点包括确保事务数据的最终一致性，保持完整的审核线索和历史记录。 审核线索本身可实现可靠的补偿操作。

此扩展使用“事件溯源”的过程是透明的。 事实上，业务流程协调程序函数中的 `await` 运算符将对业务流程协调程序线程的控制权让回给 Durable Task Framework 调度程序。 然后，该调度程序向存储提交业务流程协调程序函数计划的任何新操作（如调用一个或多个子函数或计划持久计时器）。 这个透明的提交操作会追加到业务流程实例的执行历史记录中。 历史记录存储在存储表中。 然后，提交操作向队列添加消息，以计划实际工作。 此时，可从内存中卸载业务流程协调程序函数。 如果正在使用 Azure Functions 消耗计划，将停止对其计费。  如果需要完成其他工作，可重启该函数并重新构造其状态。

如果业务流程函数需要执行其他工作（例如，收到响应消息或持久计时器到期），业务流程协调程序再次唤醒，并从头开始重新执行整个函数，以便重新生成本地状态。 如果代码在此重播过程中尝试调用函数（或执行任何其他异步工作），Durable Task Framework 会查询当前业务流程的执行历史记录。 如果发现已执行活动函数并生成某个结果，则它将重播该函数的结果，而业务流程协调程序代码继续运行。 在函数代码完成或计划了新的异步工作前，此过程一直继续。

### <a name="orchestrator-code-constraints"></a>业务流程协调程序代码约束

重播行为针对可在业务流程协调程序中编写的代码类型创建约束。 例如，业务流程协调程序代码必须具有确定性，因为该代码将被重播多次，每次必须生成相同的结果。 可在“检查点和重启”一文的[业务流程协调程序代码约束](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)部分查看完整的约束列表。

## <a name="language-support"></a>语言支持

目前，C# 是 Durable Functions 唯一支持的语言。 这包括业务流程协调程序函数和活动函数。 将来，我们将添加对 Azure Functions 支持的所有语言的支持。 若要查看其它语言支持工作的最新状态，请参阅 Azure Functions [GitHub 存储库问题列表](https://github.com/Azure/azure-functions-durable-extension/issues)。

## <a name="monitoring-and-diagnostics"></a>监视和诊断

如果使用 Application Insights 检测密钥配置函数应用，Durable Functions 扩展会自动向 [Application Insights](functions-monitoring.md) 发出结构化跟踪数据。 此跟踪数据可用于监视业务流程的行为和进度。

下面的示例展示了使用 [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics) 时，Durable Functions 在 Application Insights 门户中的外观：

![App Insights 查询结果](media/durable-functions-overview/app-insights-1.png)

每条日志项录都有许多有用的结构化数据打包到 `customDimensions` 字段中。 下面的示例展示了一个此类项目完全展开的形式。

![App Insights 查询中的 customDimensions 字段](media/durable-functions-overview/app-insights-2.png)

由于 Durable Task Framework 调度程序的重播行为，用户应该会看到重播操作的冗余日志项目。 这对了解核心引擎的重播行为非常有用。 [诊断](durable-functions-diagnostics.md)一文演示了一些示例查询，这些查询对重播日志进行筛选，以便只显示“实时”日志。

## <a name="storage-and-scalability"></a>存储和可伸缩性

Durable Functions 扩展使用 Azure 存储队列、表和 Blob 来持久保存执行历史记录状态和触发函数执行。 可使用函数应用的默认存储帐户，也可配置单独的存储帐户。 由于存储吞吐量限制，可能需要一个单独的帐户。 所编写的业务流程协调程序代码无需（也不应）与这些存储帐户中的实体进行交互。 Durable Task Framework 将实体作为实现详细信息直接进行管理。

业务流程协调程序函数通过内部队列消息计划活动函数和接收这些函数的响应。 如果在 Azure Functions 消耗计划中运行函数应用，这些队列由 [Azure Functions 缩放控制器](functions-scale.md#how-the-consumption-plan-works)进行监视，并根据需要添加新的计算实例。 如果扩大到多个 VM，则业务流程协调程序函数可能在一个 VM 上运行，而它调用的活动函数则在多个不同的 VM 上运行。 可在[性能和缩放](durable-functions-perf-and-scale.md)中找到关于 Durable Functions 的缩放行为的更多详细信息。

表存储用于存储业务流程协调程序帐户的执行历史记录。 每当有实例在特定 VM 上解除冻结时，该实例都可从表存储中获取其执行历史记录，以便可重新生成本地状态。 在表存储中提供历史记录的一项便利是可使用 [Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)等工具查看业务流程的历史记录。

![Azure 存储资源管理器屏幕截图](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> 尽管可以在表存储中轻松查看执行历史记录，但不要对此表有任何依赖。 它可能会随着 Durable Functions 扩展的演变而变化。

## <a name="known-issues-and-faq"></a>已知问题和常见问题解答

一般来说，可在 [GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues)列表中跟踪所有已知问题。 如果遇到 GitHub 中未列出的问题，请打开“新问题”并提供问题的详细说明。 即便只是想要提问，也可随意打开一个 GitHub 问题并将其标记为提问。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [继续阅读 Durable Functions 文档](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [安装 Durable Functions 扩展和示例](durable-functions-install.md)

