---
title: Durable Functions 模式和在 Azure Functions 中的技术概念
description: 了解如何在 Azure Functions 的 Durable Functions 扩展使有状态的代码执行在云中的优势。
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: e5be81efcd655f1f0361d8c00d978a81c3e6caa5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443413"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions 模式和技术概念 (Azure Functions)

Durable Functions 是的扩展[Azure Functions](../functions-overview.md)并[Azure web 作业](../../app-service/web-sites-create-web-jobs.md)。 您可以使用 Durable Functions 无服务器环境中编写有状态函数。 该扩展可用于管理状态、检查点和重启。 

此文章提供详细的行为的信息的 Durable Functions 扩展的 Azure Functions 和常见实现模式。 信息可以帮助您确定如何使用 Durable Functions 可以帮助解决开发难题。

> [!NOTE]
> Durable Functions 是不是适用于所有应用程序的 Azure Functions 的高级的扩展。 本文假定你具有中的概念非常熟悉[Azure Functions](../functions-overview.md)和无服务器应用程序开发中涉及的挑战。

## <a name="patterns"></a>模式

本部分介绍一些常见的应用程序模式，Durable Functions 可能非常有用。

### <a name="chaining"></a>模式 #1：函数链

在函数中链接模式，一系列函数按特定顺序执行。 在此模式下，一个函数的输出应用到另一个函数的输入。

![函数链模式的关系图](./media/durable-functions-concepts/function-chaining.png)

Durable Functions 可用于实现函数链接模式用于简明地，在下面的示例所示：

#### <a name="c-script"></a>C# 脚本

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> 有细微差异中编写预编译的持久函数C#中编写预编译的持久函数和C#示例中所示的脚本。 在C#预编译的函数，持久参数必须使用各自的属性进行修饰。 例如，`[OrchestrationTrigger]`属性`DurableOrchestrationContext`参数。 在C#预编译的持久函数，如果参数不正确进行修饰，运行时不能将变量注入到函数，并出现错误。 有关更多示例，请参阅[GitHub 上的 azure functions durable 扩展示例](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples)。

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

在此示例中，值`F1`， `F2`， `F3`，和`F4`是函数应用中的其他函数的名称。 可以通过使用常规命令性编码构造实现控制流。 向下，从顶部执行代码。 代码可能涉及现有语言控制流的语义，如条件语句和循环。 可以包含错误处理中的逻辑`try` / `catch` / `finally`块。

可以使用`context`参数[DurableOrchestrationContext] \(.NET\)和`context.df`要按名称调用其他函数、 传递参数，并返回函数的对象 (JavaScript)输出。 每次该代码调用`await`(C#) 或`yield`(JavaScript)，Durable Functions 框架检查点的当前函数实例的进度。 如果 VM 的进程回收在执行中途，函数实例恢复从前面`await`或`yield`调用。 有关详细信息，请参阅下一部分中，模式 #2:风扇出/扇入。

> [!NOTE]
> `context`中 JavaScript 对象表示整个[函数上下文](../functions-reference-node.md#context-object)，不仅[DurableOrchestrationContext]参数。

### <a name="fan-in-out"></a>模式 #2：风扇出/扇入

在风扇出/扇入模式，并行执行多个函数，然后等待所有的函数完成。 通常情况下，从函数返回的结果完成一些聚合操作。

![风扇的关系图扩展/风扇模式](./media/durable-functions-concepts/fan-out-fan-in.png)

对于普通函数，您可以分散通过使函数向队列发送多条消息。 扇入回来则困难得多。 若要在中，展开在普通函数中，您编写代码来跟踪时队列触发的函数结束时，以及然后存储函数输出。 

Durable Functions 扩展处理相对简单的代码使用此模式：

#### <a name="c-script"></a>C# 脚本

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

扇出操作会分发到多个实例`F2`函数。 通过使用动态任务列表跟踪工作。 .NET `Task.WhenAll` API 或 JavaScript `context.df.Task.all` API 调用时，要等待的所有调用的函数来完成。 然后，将`F2`函数输出从动态任务列表聚合和传递给`F3`函数。

在进行自动检查点`await`或`yield`上调用`Task.WhenAll`或`context.df.Task.all`确保潜在正好崩溃或重新启动不需要重启已完成的任务。

### <a name="async-http"></a>模式 #3：异步 HTTP API

异步 HTTP Api 模式解决了的协调与外部客户端长时间运行操作的状态的问题。 若要实现此模式的常用方法是通过 HTTP 调用触发长时间运行操作。 然后，将客户端重定向到的客户端轮询若要了解完成该操作后状态终结点。

![HTTP API 模式的关系图](./media/durable-functions-concepts/async-http-api.png)

Durable Functions 提供内置 Api，可简化为与长时间运行的函数执行进行交互而编写的代码。 Durable Functions 快速入门示例 ([ C# ](durable-functions-create-first-csharp.md)并[JavaScript](quickstart-js-vscode.md)) 显示可用于启动新业务流程协调程序函数实例的简单 REST 命令。 实例启动后，扩展会公开 webhook HTTP Api，查询业务流程协调程序函数状态。 

下面的示例显示了 REST 命令启动业务流程协调程序和查询其状态。 为清楚起见，实例中省略了某些细节。

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

由于 Durable Functions 运行时管理状态，不需要实现自己的状态跟踪机制。

Durable Functions 扩展拥有内置管理长时间运行的业务流程的 webhook。 通过使用你自己的函数触发器 （如 HTTP、 队列或 Azure 事件中心） 实现此模式和`orchestrationClient`绑定。 例如，可能会使用队列消息触发终止。 或者，可能会使用受 Azure Active Directory 身份验证策略而不是使用生成的密钥进行身份验证的内置 webhook 的 HTTP 触发器。

下面是如何使用 HTTP API 模式的一些示例：

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> 当你在本地开发在 JavaScript 中，若要使用方法`DurableOrchestrationClient`，您必须设置环境变量`WEBSITE_HOSTNAME`到`localhost:<port>`(例如， `localhost:7071`)。 有关此要求的详细信息，请参阅[GitHub 问题 28](https://github.com/Azure/azure-functions-durable-js/issues/28)。

在 .NET 中，[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` 参数是 `orchestrationClient` 输出绑定中的值，该绑定是 Durable Functions 扩展的一部分。 在 JavaScript 中，此对象是通过调用 `df.getClient(context)` 返回的。 这些对象提供了可用于启动、 发送到事件、 终止和查询新的或现有业务流程协调程序函数实例的方法。

在上述示例中，HTTP 触发的函数采用`functionName`从传入 URL 的值，并传递到[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)。 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_)然后绑定 API 返回响应，其中包含`Location`标头和有关实例的其他信息。 若要查找已启动实例的状态或终止该实例，可以更高版本使用的信息。

### <a name="monitoring"></a>模式 #4：监视

监视模式是一个灵活、 重复的过程，在工作流中。 示例轮询，直到满足特定条件。 可以使用正则表达式[计时器触发器](../functions-bindings-timer.md)解决基本方案，例如，定期清理作业，但其时间间隔是静态的管理实例生存期会变得复杂。 Durable Functions 可用于创建灵活的重复间隔、 任务生存期管理和从单个业务流程创建多个监视器进程。

监视模式的示例是反向早期异步 HTTP API 方案。 而不是公开外部客户端监视长时间运行操作的终结点，长时间运行的监视器使用外部终结点，并随后等待状态更改。

![监视模式的关系图](./media/durable-functions-concepts/monitor.png)

在几行代码，您可以使用 Durable Functions 创建观察任意终结点的多个监视器。 监视器可以结束执行时满足条件，或[DurableOrchestrationClient](durable-functions-instance-management.md)可以终止监视器。 您可以更改监视器的`wait`间隔基于特定条件 （例如，指数退避算法。） 

下面的代码实现基本的监视器：

#### <a name="c-script"></a>C# 脚本

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

收到请求时，会为该作业 ID 创建新的业务流程实例。 该实例会一直轮询状态，直到满足条件退出循环。 持久计时器控件轮询间隔。 然后，可以执行更多的工作，或者可以结束业务流程。 当`context.CurrentUtcDateTime`(.NET) 或`context.df.currentUtcDateTime`(JavaScript) 超出了`expiryTime`值，监视结束。

### <a name="human"></a>模式 #5：人机交互

许多自动化的过程均涉及某种类型的人机交互。 让自动化过程中的人是比较棘手，因为人们不是为高可用和云服务一样的响应速度。 自动化的过程可能会允许为此，通过使用超时和补偿逻辑。

审批流程是业务流程涉及人机交互的示例。 经理进行审批可能需要超过特定金额的费用报表。 如果该管理器 （也许 manager 去度假） 的 72 小时内未批准费用报表，则上报过程启动若要从其他人 （可能是经理的经理） 获取的批准。

![人机交互模式的关系图](./media/durable-functions-concepts/approval.png)

通过使用业务流程协调程序函数，可以在此示例中实现该模式。 使用业务流程协调程序[持久计时器](durable-functions-timers.md)到审批请求。 如果发生超时，他们会上报业务流程协调程序。 业务流程协调程序等待[外部事件](durable-functions-external-events.md)，如生成的人机交互的通知。

这些示例创建一个审批过程进行演示的人机交互模式：

#### <a name="c-script"></a>C# 脚本

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

若要创建持久计时器，请调用`context.CreateTimer`(.NET) 或`context.df.createTimer`(JavaScript)。 通知由 `context.WaitForExternalEvent` (.NET) 或 `context.df.waitForExternalEvent` (JavaScript) 接收。 然后， `Task.WhenAny` (.NET) 或`context.df.Task.any`(JavaScript) 调用以确定是上报 （首先发生超时） 还是处理审批 （超时前收到审批）。

外部客户端可以将事件通知传递到等待业务流程协调程序函数通过使用[内置 HTTP Api](durable-functions-http-api.md#raise-event)或使用[DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_)中的 API另一个函数：

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>技术

在后台，Durable Functions 扩展构建的[Durable Task Framework](https://github.com/Azure/durabletask)，用于生成持久任务业务流程的 GitHub 上的开放源代码库。 Azure Functions 是 Azure WebJobs 的无服务器演进，Durable Functions 也是 Durable Task Framework 的无服务器演进。 Microsoft 和其他组织使用 Durable Task Framework 大量自动执行关键任务的过程。 它天生就很适合无服务器 Azure Functions 环境。

### <a name="event-sourcing-checkpointing-and-replay"></a>事件溯源、检查点和重播

业务流程协调程序函数通过使用可靠地维护其执行状态[事件溯源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)设计模式。 而不是直接存储业务流程的当前状态，Durable Functions 扩展使用只追加存储来记录函数业务流程获取的操作的完整系列。 仅限追加存储具有与"转储"完整的运行时状态相比的诸多优点。 优点包括更高的性能、 可伸缩性和响应能力。 您还获得最终一致性事务数据和完整审核记录和历史记录。 审核跟踪支持可靠的补偿操作。

Durable Functions 使用事件溯源以透明方式。 在后台`await`(C#) 或`yield`中的业务流程协调程序函数 (JavaScript) 运算符将业务流程协调程序线程的控制权返还给 Durable Task Framework 调度程序。 然后，该调度程序向存储提交业务流程协调程序函数计划的任何新操作（如调用一个或多个子函数或计划持久计时器）。 透明的提交操作将追加到业务流程实例的执行历史记录。 历史记录存储在存储表中。 然后，提交操作向队列添加消息，以计划实际工作。 此时，可从内存中卸载业务流程协调程序函数。 

如果使用 Azure Functions 消耗计划会停止业务流程协调程序函数的计费。 当没有更多工作要做，函数会重新启动，并且其状态并重新构造。

业务流程函数时提供更多工作要做 （例如，接收到响应消息或持久计时器已过期），业务流程协调程序唤醒并重新执行整个函数从开始到重新生成本地状态。 

重播，如果该代码尝试调用的函数 (或执行任何其他异步工作)，Durable Task Framework 会查询当前业务流程的执行历史记录。 如果它发现[活动函数](durable-functions-types-features-overview.md#activity-functions)已执行并生成一个结果，则它将重播该函数的结果和业务流程协调程序代码继续运行。 重播将继续完成函数代码或等到计划新的异步工作。

### <a name="orchestrator-code-constraints"></a>业务流程协调程序代码约束

业务流程协调程序代码的重播行为创建约束的类型可以在业务流程协调程序函数中编写的代码。 例如，业务流程协调程序代码必须具有确定性因为它将被重播多次，并且它必须每次生成相同的结果。 有关约束的完整列表，请参阅[业务流程协调程序代码约束](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)。

## <a name="monitoring-and-diagnostics"></a>监视和诊断

Durable Functions 扩展可自动生成为结构化的跟踪数据[Application Insights](../functions-monitoring.md)如果 function app 设置使用 Azure Application Insights 检测密钥。 跟踪数据可用于监视操作和业务流程的进度。

下面是示例的跟踪事件 Durable Functions 在 Application Insights 门户中使用时[Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Application Insights 查询结果](./media/durable-functions-concepts/app-insights-1.png)

您可以找到有用的结构化的数据中`customDimensions`字段中每个日志条目。 下面是项的一个完全展开的示例：

![Application Insights 查询中的 customDimensions 字段](./media/durable-functions-concepts/app-insights-2.png)

由于 Durable Task Framework 调度程序的重播行为，用户应该会看到重播操作的冗余日志项目。 冗余日志项目可以帮助您了解核心引擎的重播行为。 [诊断](durable-functions-diagnostics.md)文章显示了筛选出重播日志，以便您可以看到只需"实时"日志的示例查询。

## <a name="storage-and-scalability"></a>存储和可伸缩性

Durable Functions 扩展使用队列、 表和 blob 在 Azure 存储中保存执行历史记录状态和触发函数执行。 可用于 function app 的默认存储帐户，或者可以配置单独的存储帐户。 您可能希望基于存储吞吐量限制的单独帐户。 您编写的业务流程协调程序代码不会与这些存储帐户中的实体进行交互。 Durable Task Framework 直接作为实现详细信息，用于管理实体。

业务流程协调程序函数通过内部队列消息计划活动函数和接收这些函数的响应。 当函数应用在 Azure Functions 消耗计划中，在运行时[Azure Functions 缩放控制器](../functions-scale.md#how-the-consumption-plan-works)监视这些队列。 根据需要添加了新的计算实例。 如果扩大到多个 Vm，业务流程协调程序函数可能运行在一个 VM，同时业务流程协调程序函数调用可能在许多不同的 Vm 运行的活动函数上。 Durable Functions 的缩放行为的详细信息，请参阅[性能和规模](durable-functions-perf-and-scale.md)。

业务流程协调程序帐户的执行历史记录存储在表存储。 只要在特定 VM 上解除冻结实例，业务流程协调程序会从表存储中提取的执行历史记录以便它可以重新生成其本地状态。 历史记录表存储中提供的方便方面是，可以使用像这样的工具[Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)若要查看您的业务流程的历史记录。

存储 blob 主要用作租用机制来协调跨多个 Vm 的业务流程实例的扩展。 存储 blob 保存数据的大无法直接在表或队列中存储的消息。

![Azure 存储资源管理器的屏幕截图](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> 虽然很简单且方便地查看表存储中的执行历史记录，但不要对此表进行任何依赖项。 随着 Durable Functions 扩展的发展，可能会更改的表。

## <a name="known-issues"></a>已知问题

应在 [GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues)列表中跟踪所有已知问题。 如果遇到问题，并在 GitHub 中找不到此问题，请打开一个新问题。 包含问题的详细的说明。

## <a name="next-steps"></a>后续步骤

若要了解有关 Durable Functions 的详细信息，请参阅[Durable Functions 函数类型和功能](durable-functions-types-features-overview.md)。 

开始操作：

> [!div class="nextstepaction"]
> [创建第一个 Durable Function](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
