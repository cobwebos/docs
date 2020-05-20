---
title: 持久业务流程 - Azure Functions
description: Azure Durable Functions 的业务流程功能简介。
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79290105"
---
# <a name="durable-orchestrations"></a>持久业务流程

Durable Functions 是 [Azure Functions](../functions-overview.md) 的一个扩展。 可以使用业务流程协调程序函数来协调函数应用中其他持久函数的执行。  业务流程协调程序函数具有以下特征：

* 业务流程协调程序函数使用过程代码来定义函数工作流。 不需要声明性的架构或设计器。
* 业务流程协调程序函数可以同步和异步调用其他持久函数。 被调用函数的输出可以可靠保存到本地变量。
* 业务流程协调程序函数是持久且可靠的。 当函数处于“等待”或“生成”状态时，可自动对执行进度设置检查点。 回收进程或重启 VM 时，从来不会丢失本地状态。
* 业务流程协调程序函数可以长时间运行。 业务流程实例的总生存期可以是几秒、几天、几月或者永不结束。 

本文概述业务流程协调程序功能，及其如何帮助解决各种应用开发难题。 如果你不熟悉 Durable Functions 应用中可用的函数类型，请先阅读[持久函数类型](durable-functions-types-features-overview.md)一文。

## <a name="orchestration-identity"></a>业务流程标识

业务流程的每个实例都有一个实例标识符（也称为“实例 ID”）。   默认情况下，每个实例 ID 都是自动生成的 GUID。 但是，实例 ID 也可以是用户生成的任何字符串值。 每个业务流程实例 ID 在[任务中心](durable-functions-task-hubs.md)内必须唯一。

下面是有关实例 ID 的一些规则：

* 实例 ID 必须为 1 到 256 个字符。
* 实例 ID 不能以 `@` 开头。
* 实例 ID 不能包含 `/`、`\`、`#` 或 `?` 字符。
* 实例 ID 不能包含控制字符。

> [!NOTE]
> 通常建议尽可能使用自动生成的实例 ID。 用户生成的实例 ID 适用于业务流程实例与某些外部应用程序特定的实体（例如采购订单或文档）之间存在一对一映射的情况。

业务流程的实例 ID 是大多数[实例管理操作](durable-functions-instance-management.md)的必需参数。 它们对于诊断也很重要，例如，在 Application Insights 中[搜索业务流程跟踪数据](durable-functions-diagnostics.md#application-insights)以进行故障排除或分析。 出于此原因，建议将生成的实例 ID 保存到可在今后方便引用的某个外部位置（例如数据库或应用程序日志中）。

## <a name="reliability"></a>可靠性

业务流程协调程序函数使用[事件溯源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)设计模式可靠地维护自身的执行状态。 Durable Task Framework 使用仅限追加的存储来记录函数业务流程执行的一系列完整操作，而不是直接存储业务流程的当前状态。 与“转储”整个运行时状态相比，仅限追加的存储具有很多优点。 优点包括提高性能、可伸缩性和响应能力。 此外，它还提供事务数据的最终一致性，以及完整的审核线索和历史记录。 审核线索支持可靠的补偿操作。

Durable Functions 以透明方式使用事件溯源。 在幕后，业务流程协调程序函数中的 `await` (C#) 或 `yield` (JavaScript) 运算符将对业务流程协调程序线程的控制权让回给 Durable Task Framework 调度程序。 然后，该调度程序向存储提交业务流程协调程序函数计划的任何新操作（如调用一个或多个子函数或计划持久计时器）。 透明的提交操作会追加到业务流程实例的执行历史记录中。 历史记录存储在存储表中。 然后，提交操作向队列添加消息，以计划实际工作。 此时，可从内存中卸载业务流程协调程序函数。

如果业务流程函数需要执行其他工作（例如，收到响应消息或持久计时器到期），业务流程协调程序将会唤醒，并从头开始重新执行整个函数，以重新生成本地状态。 在重播过程中，如果代码尝试调用函数（或执行任何其他异步工作），Durable Task Framework 会查询当前业务流程的执行历史记录。 如果该扩展发现[活动函数](durable-functions-types-features-overview.md#activity-functions)已执行并已生成结果，则会回放该函数的结果并且业务流程协调程序代码继续运行。 在函数代码完成或计划了新的异步工作之前，重放会一直继续。

> [!NOTE]
> 要使重播模式正常可靠工作，业务流程协调程序函数代码必须是确定性的。  有关业务流程协调程序函数的代码限制的详细信息，请参阅[业务流程协调程序函数代码约束](durable-functions-code-constraints.md)主题。

> [!NOTE]
> 如果业务流程协调程序函数发出日志消息，重播行为可能导致发出重复的日志消息。 请参阅[日志记录](durable-functions-diagnostics.md#logging)主题来详细了解此行为的发生原因及其解决方法。

## <a name="orchestration-history"></a>业务流程历史记录

Durable Task Framework 的事件溯源行为与编写的业务流程协调程序函数代码密切相关。 假设你有一个活动链接业务流程协调程序函数，如以下业务流程协调程序函数：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

---

执行到每条 `await` (C#) 或 `yield` (JavaScript) 语句时，Durable Task Framework 会在某个持久存储后端（通常是 Azure 表存储）中创建该函数的执行状态检查点。 此状态称为“业务流程历史记录”。 

### <a name="history-table"></a>历史记录表

一般而言，Durable Task Framework 会在每个检查点位置执行以下操作：

1. 将执行历史记录保存到 Azure 存储表中。
2. 将业务流程协调程序想要调用的函数的相关消息排队。
3. 将业务流程协调程序本身的消息排队 &mdash; 例如，持久计时器消息。

完成检查点之后，在业务流程协调程序函数需要执行其他工作之前，可以从内存中任意删除该函数。

> [!NOTE]
> 在将数据保存到表存储和队列的间隔期限内，Azure 存储不提供任何事务保证。 若要处理错误，Durable Functions 存储提供程序会使用“最终一致性”模式。  这些模式可确保在发生崩溃时不会丢失数据，或者在创建检查点的中途不会断开连接。

完成后，前面所示的函数历史记录在 Azure 表存储中如下表所示（为方便演示，此处采用了缩写）：

| PartitionKey (InstanceId)                     | EventType             | 时间戳               | 输入 | 名称             | 结果                                                    | 状态 |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | 已完成           |

有关列值的一些注释：

* **PartitionKey**：包含业务流程的实例 ID。
* **EventType**：表示事件的类型。 可为以下类型之一：
  * **OrchestrationStarted**：业务流程协调程序函数已从 await（等待）状态恢复，或者正在首次运行。 `Timestamp` 列用于填充 `CurrentUtcDateTime` (.NET) 和 `currentUtcDateTime` (JavaScript) API 的确定性值。
  * **ExecutionStarted**：业务流程协调程序函数已开始首次执行。 此事件也包含 `Input` 列中输入的函数。
  * **TaskScheduled**：已计划活动函数。 `Name` 列中已捕获该活动函数的名称。
  * **TaskCompleted**：已完成活动函数。 `Result` 列中提供了该函数的结果。
  * **TimerCreated**：已创建持久计时器。 `FireAt` 列包含计时器过期时的 UTC 计划时间。
  * **TimerFired**：持久计时器已触发。
  * **EventRaised**：已将外部事件发送到业务流程实例。 `Name` 列捕获事件的名称，`Input` 列捕获事件的有效负载。
  * **OrchestratorCompleted**：业务流程协调程序函数处于等待状态。
  * **ContinueAsNew**：业务流程协调程序函数已完成，并已使用新状态重启自身。 `Result` 列包含用作已重启实例中的输入的值。
  * **ExecutionCompleted**：业务流程协调程序函数已运行并已完成（或失败）。 该函数的输出或错误详细信息存储在 `Result` 列中。
* **Timestamp**：历史记录事件的 UTC 时间戳。
* **Name**：调用的函数的名称。
* **Input**：函数的 JSON 格式输入。
* **Result**：函数的输出，即其返回值。

> [!WARNING]
> 尽管此表可以用作有效的调试工具，但不要对它有任何依赖。 它可能会随着 Durable Functions 扩展的演变而变化。

每当函数从 `await` (C#) 或 `yield` (JavaScript) 恢复时，Durable Task Framework 会从头开始重新运行业务流程协调程序函数。 每次重新运行时，它会查询执行历史记录，确定当前的异步操作是否已发生。  如果该操作已发生，该框架会立即重播该操作的输出，并转到下一个 `await` (C#) 或 `yield` (JavaScript)。 此过程会持续到整个历史记录被重播为止。 重播当前历史记录后，本地变量已还原到其先前值。

## <a name="features-and-patterns"></a>功能和模式

以下部分介绍业务流程协调程序函数的功能和模式。

### <a name="sub-orchestrations"></a>子业务流程

业务流程协调程序函数可以调用活动函数，但也可以调用其他业务流程协调程序函数。 例如，可以基于业务流程协调程序函数库构建更大的业务流程。 或者，你可以并行运行某个业务流程协调程序函数的多个实例。

有关详细信息和示例，请参阅[子业务流程](durable-functions-sub-orchestrations.md)一文。

### <a name="durable-timers"></a>持久计时器

业务流程可以计划持久计时器来实现延迟或设置处理异步操作时的超时。  请在业务流程协调程序函数中使用持久计时器，而不要使用 `Thread.Sleep` 和 `Task.Delay` (C#) 或 `setTimeout()` 和 `setInterval()` (JavaScript)。

有关详细信息和示例，请参阅[持久计时器](durable-functions-timers.md)一文。

### <a name="external-events"></a>外部事件

业务流程协调程序函数可以等待外部事件来更新业务流程实例。 此项 Durable Functions 功能通常用于处理人机交互或其他外部回调。

有关详细信息和示例，请参阅[外部事件](durable-functions-external-events.md)一文。

### <a name="error-handling"></a>错误处理。

业务流程协调程序函数可以使用编程语言的错误处理功能。 业务流程代码中支持诸如 `try`/`catch` 的现有模式。

业务流程协调程序函数还可以将重试策略添加到它们调用的活动或子业务流程协调程序函数。 如果某个活动或子业务流程协调程序函数失败并出现异常，则指定的重试策略可以自动延迟并重试执行，直到达到指定的次数。

> [!NOTE]
> 如果业务流程协调程序函数中出现未经处理的异常，则业务流程实例将以 `Failed` 状态完成。 业务流程实例在失败后不可重试。

有关详细信息和示例，请参阅[错误处理](durable-functions-error-handling.md)一文。

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>关键节（Durable Functions 2.x，当前仅限 .NET）

业务流程实例是单线程的，因此无需考虑业务流程内部的争用情况。  但是，当业务流程与外部系统交互时，可能会出现争用情况。 若要在与外部系统交互时缓解争用情况，业务流程协调程序函数可以使用 .NET 中的 `LockAsync` 方法定义关键节。

以下示例代码演示了一个定义关键节的业务流程协调程序函数。 它使用 `LockAsync` 方法进入关键节。 此方法要求向某个持久管理锁状态的[持久实体](durable-functions-entities.md)传递一个或多个引用。 此业务流程的单个实例每次只能执行关键节中的代码。

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

`LockAsync` 获取持久锁，并返回一个在释放时结束关键节的 `IDisposable`。 可将此 `IDisposable` 结果与 `using` 块一起使用，以获取关键节的语法表示形式。 当业务流程协调程序函数进入关键节时，只有一个实例可以执行该代码块。 尝试进入关键节的任何其他实例将被阻止，直到上一个实例退出关键节。

关键节功能对于协调持久实体的更改也很有用。 有关关键节的详细信息，请参阅[持久实体的“实体协调”](durable-functions-entities.md#entity-coordination)主题。

> [!NOTE]
> 关键节在 Durable Functions 2.0 及更高版本中可用。 目前，只有 .NET 业务流程实现此功能。

### <a name="calling-http-endpoints-durable-functions-2x"></a>调用 HTTP 终结点 (Durable Functions 2.x)

根据[协调程序函数代码约束](durable-functions-code-constraints.md)中所述，不允许业务流程协调程序函数执行 I/O。 此项限制的典型解决方法是将任何需要执行 I/O 的代码包装在某个活动函数中。 与外部系统交互的业务流程经常使用活动函数发出 HTTP 调用，并将结果返回给业务流程。

# <a name="c"></a>[C#](#tab/csharp)

若要简化这种常见模式，业务流程协调程序函数可以使用 `CallHttpAsync` 方法直接调用 HTTP API。

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

除了支持基本请求/响应模式外，该方法还支持自动处理常见的异步 HTTP 202 轮询模式，并支持使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)通过外部服务进行身份验证。

有关详细信息和示例，请参阅 [HTTP 功能](durable-functions-http-features.md)一文。

> [!NOTE]
> 在 Durable Functions 2.0 及更高版本中可以直接从业务流程协调程序函数调用 HTTP 终结点。

### <a name="passing-multiple-parameters"></a>传递多个参数

无法直接将多个参数传递给一个活动函数。 建议传入对象或复合对象的数组。

# <a name="c"></a>[C#](#tab/csharp)

在 .NET 中，还可以使用 [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) 对象。 以下示例使用了 [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples) 添加的 [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) 的新功能：

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>业务流程协调程序

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="activity"></a>活动

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [业务流程协调程序代码约束](durable-functions-code-constraints.md)
