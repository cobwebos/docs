---
title: Durable Functions 中的诊断 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 扩展诊断问题。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231456"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure Durable Functions 中的诊断

可以使用多个选项通过 [Durable Functions](durable-functions-overview.md) 诊断问题。 其中有些选项与适用于正则函数的选项相同，还有一些选项是 Durable Functions 特有的。

## <a name="application-insights"></a>Application Insights

使用 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 是在 Azure Functions 中执行诊断和监视的建议方法。 这同样适用于 Durable Functions。 有关如何在函数应用中利用 Application Insights 的概述，请参阅[监视 Azure Functions](../functions-monitoring.md)。

Azure Functions Durable 扩展还会发出跟踪事件，用于跟踪业务流程的端到端执行。 These tracking events can be found and queried using the [Application Insights Analytics](../../azure-monitor/app/analytics.md) tool in the Azure portal.

### <a name="tracking-data"></a>跟踪数据

业务流程实例的每个生命周期事件会导致将一个跟踪事件写入 Application Insights 中的**跟踪**集合。 此事件包含带有多个字段的 **customDimensions** 有效负载。  字段名称的前面都附有 `prop__`。

* **hubName**：运行业务流程的任务中心的名称。
* **appName**：函数应用的名称。 This field is useful when you have multiple function apps sharing the same Application Insights instance.
* **slotName**：运行当前函数应用的[部署槽位](../functions-deployment-slots.md)。 This field is useful when you leverage deployment slots to version your orchestrations.
* **functionName**：业务流程协调程序或活动函数的名称。
* **functionType**：函数的类型，例如“业务流程协调程序”或“活动”。
* **instanceId**：业务流程实例的唯一 ID。
* **state**：实例的生命周期执行状态。 有效值包括：
  * **Scheduled**：函数已计划执行，但尚未开始运行。
  * **Started**：函数已开始运行，但尚未进入等待或完成状态。
  * **Awaited**：业务流程协调程序已计划某项工作，正在等待该工作完成。
  * **Listening**：业务流程协调程序正在侦听外部事件通知。
  * **Completed**：函数已成功完成。
  * **Failed**：函数失败并出错。
* **reason**：与跟踪事件关联的其他数据。 例如，如果某个实例正在等待外部事件通知，则此字段指示该实例正在等待的事件的名称。 If a function has failed, this field will contain the error details.
* **isReplay**：指示跟踪事件是否用于重播执行的布尔值。
* **extensionVersion**：持久任务扩展的版本。 The version information is especially important data when reporting possible bugs in the extension. 如果长时间运行的实例在运行时发生更新，它可能会报告多个版本。
* **sequenceNumber**：事件的执行序列号。 与时间戳组合使用可以帮助按执行时间对事件进行排序。 *请注意，如果主机在实例正在运行时重新启动，则此数字将重置为零，因此始终先按时间戳然后按 sequenceNumber 排序很重要。*

The verbosity of tracking data emitted to Application Insights can be configured in the `logger` (Functions 1.x) or `logging` (Functions 2.0) section of the `host.json` file.

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

默认情况下，会发出所有非重播跟踪事件。 可通过将 `Host.Triggers.DurableTask` 设置为 `"Warning"` 或 `"Error"` 来减少数据量，在这种情况下，只会在发生异常情况时发出跟踪事件。

若要启用发出详细业务流程重播事件，可以在 `host.json` 文件中的 `durableTask` 下将 `LogReplayEvents` 设置为 `true`，如下所示：

#### <a name="functions-10"></a>Functions 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 默认情况下，Azure Functions 运行时会对 Application Insights 遥测数据采样，以免过度频繁地发出数据。 如果在短时间内发生了许多的生命周期事件，此行为可能会导致跟踪信息丢失。 [Azure Functions 监视文章](../functions-monitoring.md#configure-sampling)介绍了如何配置此行为。

### <a name="single-instance-query"></a>单实例查询

以下查询显示 [Hello Sequence](durable-functions-sequence.md) 函数业务流程的单个实例的历史跟踪数据。 该查询是使用 [Application Insights 查询语言 (AIQL)](https://aka.ms/LogAnalyticsLanguageReference) 编写的。 它会筛选出重播执行，以便仅显示逻辑执行路径。 可以通过按 `timestamp` 和 `sequenceNumber` 排序来安排事件顺序，如以下查询中所示：

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

结果是显示业务流程执行路径的跟踪事件的列表，包括所有活动函数，按执行时间以升序排序。

![Application Insights 查询](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>实例摘要查询

以下查询显示在指定时间范围中运行的所有业务流程实例的状态。

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

结果是实例 ID 的列表及其当前运行时状态。

![Application Insights 查询](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>日志记录

直接从业务流程协调程序函数写入日志时，必须注意业务流程协调程序的重播行为。 例如，考虑以下业务流程协调程序函数：

### <a name="precompiled-c"></a>预编译 C#

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C# 脚本

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

The resulting log data is going to look something like the following example output:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> 请记住，尽管日志声明要调用 F1、F2 和 F3，但实际上仅在首次遇到这些函数时才调用这些函数。 在重播期间发生的后续调用将被跳过，输出将重播到业务流程协调程序逻辑。

如果只想针对非重播执行记录日志，可以编写一个条件表达式，规定仅当 `IsReplaying` 为 `false` 时才记录日志。 沿用上面的示例，不过这一次要执行重播检查。

#### <a name="precompiled-c"></a>预编译 C#

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Starting in Durable Functions 2.0, .NET orchestrator functions also have the option to create an `ILogger` that automatically filters out log statements during replay. This automatic filtering is done using the `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` API.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

With the previously mentioned changes, the log output is as follows:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="custom-status"></a>自定义状态

使用自定义业务流程状态，可以为业务流程协调程序函数设置自定义状态值。 此状态是通过 HTTP 状态查询 API 或 `IDurableOrchestrationClient.GetStatusAsync` API 提供的。 自定义业务流程状态为业务流程协调程序函数实现了更丰富的监视。 例如，业务流程协调程序函数代码可以包括 `IDurableOrchestrationContext.SetCustomStatus` 调用来更新长时间运行的操作的进度。 然后，客户端（例如网页或其他外部系统）可以定期查询 HTTP 状态查询 API 以获得更丰富的进度信息。 下面提供了使用 `IDurableOrchestrationContext.SetCustomStatus` 的示例：

### <a name="precompiled-c"></a>预编译 C#

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

在业务流程正在运行时，外部客户端可以提取此自定义状态：

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

客户端将收到以下响应：

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> 自定义状态有效负载限制为 16 KB 的 UTF-16 JSON 文本，因为它需要能够容纳在 Azure 表存储列中。 如果需要更大的有效负载，可以使用外部存储。

## <a name="debugging"></a>调试

Azure Functions 支持直接调试函数代码，Durable Functions 承袭了这项支持，不管它是在 Azure 中还是在本地运行。 但是，调试时需注意几种行为：

* **Replay**: Orchestrator functions regularly [replay](durable-functions-orchestrations.md#reliability) when new inputs are received. This behavior means a single *logical* execution of an orchestrator function can result in hitting the same breakpoint multiple times, especially if it is set early in the function code.
* **Await**: Whenever an `await` is encountered in an orchestrator function, it yields control back to the Durable Task Framework dispatcher. If it is the first time a particular `await` has been encountered, the associated task is *never* resumed. Because the task never resumes, stepping *over* the await (F10 in Visual Studio) is not possible. 仅当任务正在重播时，才能跳过。
* **Messaging timeouts**: Durable Functions internally uses queue messages to drive execution of orchestrator, activity, and entity functions. 在多 VM 环境中，长时间中断调试可能会使另一个 VM 拾取消息，从而导致重复执行。 正则队列触发器函数也存在此行为，但必须在此上下文中指出，因为队列属于实现细节。
* **Stopping and starting**: Messages in Durable functions persist between debug sessions. If you stop debugging and terminate the local host process while a durable function is executing, that function may re-execute automatically in a future debug session. This behavior can be confusing when not expected. Clearing all messages from the [internal storage queues](durable-functions-perf-and-scale.md#internal-queue-triggers) between debug sessions is one technique to avoid this behavior.

> [!TIP]
> When setting breakpoints in orchestrator functions, if you want to only break on non-replay execution, you can set a conditional breakpoint that breaks only if `IsReplaying` is `false`.

## <a name="storage"></a>存储空间

默认情况下，Durable Functions 在 Azure 存储中存储状态。 This behavior means you can inspect the state of your orchestrations using tools such as [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Azure Storage Explorer screenshot](./media/durable-functions-diagnostics/storage-explorer.png)

此工具非常适合用于调试，因为可以看到业务流程所处的确切状态。 此外，还可以检查队列中的消息，了解哪项工作处于挂起状态（或停滞在某种状态）。

> [!WARNING]
> 尽管可以在表存储中方便查看执行历史记录，但不要对此表有任何依赖。 它可能会随着 Durable Functions 扩展的演变而变化。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Learn more about monitoring in Azure Functions](../functions-monitoring.md)
