---
title: 在 Durable Functions 中管理实例 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中管理实例。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 07a96fdd6350d8db38a92c23e510afb05f7416fb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277747"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的实例

如果你使用 Azure Functions 的[Durable Functions](durable-functions-overview.md)扩展，或想要开始执行此操作，请确保充分利用它。 您可以通过了解有关如何管理 Durable Functions 业务流程实例的详细信息，对其进行优化。 本文详细介绍每项实例管理操作。

例如，可以启动和终止实例，还可以查询实例，包括通过筛选器查询所有实例和查询实例的功能。 此外，你可以将事件发送到实例，等待业务流程完成，并检索 HTTP 管理 webhook Url。 本文还介绍了其他管理操作，包括倒带实例、清除实例历史记录和删除任务中心。

在 Durable Functions 中，可以选择如何实现上述每个管理操作。 本文提供了对 .NET （C#）和 JavaScript 使用[Azure Functions Core Tools](../functions-run-local.md)的示例。

## <a name="start-instances"></a>启动实例

必须能够启动业务流程实例。 当在另一个函数的触发器中使用 Durable Functions 绑定时，通常会执行此操作。

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)上的 `StartNewAsync` （.net）或 `startNew` （JavaScript）方法将启动一个新的实例。 在内部，此方法将消息排队到控制队列中，然后使用指定名称触发函数开始，该函数使用[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)。

当业务流程成功计划时，此异步操作完成。

用于启动新业务流程实例的参数如下所示：

* **Name**：要计划的业务流程协调程序函数的名称。
* **Input**：应作为输入传递给业务流程协调程序函数的任何 JSON 可序列化数据。
* **InstanceId**：（可选）实例的唯一 ID。 如果未指定此参数，则方法将使用随机 ID。

> [!TIP]
> 为实例 ID 使用随机标识符。 当你在多个 Vm 上缩放业务流程协调程序函数时，随机实例 Id 有助于确保负载分布相等。 如果 ID 必须来自外部源，或者在实现[单一实例协调](durable-functions-singletons.md)器模式时，应使用非随机实例 id。

下面的代码是启动新业务流程实例的示例函数：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>除非另行指定，否则此页上的示例将使用带有以下函数的 HTTP 触发器。

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 此示例针对版本 2.x Durable Functions。 在版本1.x 中，使用 `orchestrationClient` 而不是 `durableClient`。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable start-new` 命令直接启动实例。 它采用了以下参数：

* **`function-name` （必需）** ：要启动的函数的名称。
* **`input` （可选）** ：对函数的输入（内联或通过 JSON 文件）。 对于 "文件"，请使用 `@`（如 `@path/to/file.json`）将前缀添加到文件的路径。
* **`id`（可选）** ：业务流程实例的 ID。 如果未指定此参数，则该命令使用随机 GUID。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认值为 DurableFunctionsHub。 还可以使用 durableTask： HubName 在[host](durable-functions-bindings.md#host-json)中设置此项。

> [!NOTE]
> 核心工具命令假设您从函数应用程序的根目录运行它们。 如果显式提供 `connection-string-setting` 和 `task-hub-name` 参数，则可以从任何目录运行这些命令。 虽然你可以在没有运行函数应用主机的情况下运行这些命令，但你可能会发现，除非主机正在运行，否则无法观察某些效果。 例如，`start-new` 命令将启动消息排队到目标任务中心，但业务流程实际上不会运行，除非存在运行的可处理消息的函数应用主机进程。

以下命令将启动名为 HelloWorld 的函数，并向其传递 `counter-data.json` 的文件的内容：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查询实例

在管理业务流程的过程中，您很可能需要收集有关业务流程实例状态的信息（例如，它是正常完成还是失败）。

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)上的 `GetStatusAsync` （.net）或 `getStatus` （JavaScript）方法会查询业务流程实例的状态。

它采用 `instanceId`（必需）、`showHistory`（可选）、`showHistoryOutput`（可选）和 `showInput`（可选）作为参数。

* **`showHistory`** ：如果设置为 `true`，则响应将包含执行历史记录。
* **`showHistoryOutput`** ：如果设置为 `true`，则执行历史记录将包含活动输出。
* **`showInput`** ：如果设置为 `false`，则响应不会包含函数的输入。 默认值是 `true`。

此方法返回包含以下属性的对象：

* **Name**：业务流程协调程序函数的名称。
* **InstanceId**：业务流程的实例 ID（应与 `instanceId` 输入相同）。
* **CreatedTime**：业务流程协调程序函数开始运行的时间。
* **LastUpdatedTime**：上次创建业务流程检查点的时间。
* **Input**：函数的输入，采用 JSON 值形式。 如果 `showInput` 为 false，则不填充此字段。
* **CustomStatus**：JSON 格式的自定义业务流程状态。
* **Output**：函数的输出，采用 JSON 值形式（如果该函数已完成）。 如果业务流程协调程序函数失败，此属性将包括失败的详细信息。 如果业务流程协调程序函数已终止，则此属性包括终止原因（如果有）。
* **RuntimeStatus**：以下值之一：
  * **挂起**：实例已计划但尚未开始运行。
  * **Running**：实例已开始运行。
  * **Completed**：实例已正常完成。
  * **ContinuedAsNew**：实例已重启自身并生成了新历史记录。 此状态为暂时性状态。
  * **Failed**：实例失败并出错。
  * **Terminated**：实例突然停止。
* **History**：业务流程的执行历史记录。 仅当 `showHistory` 设置为 `true` 时，才填充此字段。

如果实例不存在，此方法将返回 `null` （.NET）或 `undefined` （JavaScript）。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以通过使用[Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` 命令，直接获取业务流程实例的状态。 它采用了以下参数：

* **`id` （必需）** ：业务流程实例的 ID。
* **`show-input` （可选）** ：如果设置为 `true`，则响应包含函数的输入。 默认值是 `false`。
* **`show-output` （可选）** ：如果设置为 `true`，则响应包含函数的输出。 默认值是 `false`。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 还可以使用 durableTask： HubName 在[host json](durable-functions-bindings.md#host-json)中设置它。

以下命令将检索具有0ab8c55a66644d68a3a8b220b12d209c 的业务流程实例 ID 的实例的状态（包括输入和输出）。 它假定你正在运行函数应用的根目录中的 `func` 命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

您可以使用 `durable get-history` 命令检索业务流程实例的历史记录。 它采用了以下参数：

* **`id` （必需）** ：业务流程实例的 ID。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 还可以使用 durableTask： HubName 在 host json 中设置它。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查询所有实例

您可能会发现一次查询所有实例，而不是在业务流程中查询一个实例。

可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法查询所有业务流程实例的状态。 在 .NET 中，可以传递 `CancellationToken` 对象，以防您要取消该对象。 该方法返回具有与带参数的 `GetStatusAsync` 方法相同属性的对象。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以通过使用[Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` 命令直接查询实例。 它采用了以下参数：

* **`top`（可选）** ：此命令支持分页。 此参数对应于每个请求检索的实例数。 默认值为 10。
* **`continuation-token` （可选）** ：用于指示要检索的实例的页或部分的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 还可以使用 durableTask： HubName 在[host json](durable-functions-bindings.md#host-json)中设置它。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>带有筛选器的查询实例

如果你确实不需要标准实例查询可以提供的所有信息，该怎么办？ 例如，如果你只是想要查找业务流程的创建时间还是业务流程运行时状态？ 可以通过应用筛选器来缩小查询范围。

使用 `GetStatusAsync` （.NET）或 `getStatusBy` （JavaScript）方法获取与一组预定义筛选器匹配的业务流程实例的列表。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在 Azure Functions Core Tools 中，还可以将 `durable get-instances` 命令与筛选器一起使用。 除了上述 `top`、`continuation-token`、`connection-string-setting`和 `task-hub-name` 参数外，还可以使用三个筛选器参数（`created-after`、`created-before`和 `runtime-status`）。

* **`created-after`（可选）** ：检索在此日期/时间 (UTC) 之后创建的实例。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）** ：检索在此日期/时间 (UTC) 之前创建的实例。 接受 ISO 8601 格式的日期时间。
* **`runtime-status` （可选）** ：检索具有特定状态的实例（例如，"正在运行" 或 "已完成"）。 可以提供多个状态（用空格分隔）。
* **`top`（可选）** ：每个请求检索的实例数。 默认值为 10。
* **`continuation-token` （可选）** ：用于指示要检索的实例的页或部分的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 还可以使用 durableTask： HubName 在[host json](durable-functions-bindings.md#host-json)中设置它。

如果未提供任何筛选器（`created-after`、`created-before`或 `runtime-status`），则该命令只检索 `top` 实例，而不考虑运行时状态或创建时间。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>终止实例

如果某一业务流程实例花费了太长时间运行，或者您只是出于任何原因而需要停止它，则可以选择终止它。

您可以使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `TerminateAsync` （.net）或 `terminate` （JavaScript）方法来终止实例。 这两个参数是一个 `instanceId` 和一个 `reason` 字符串，它们写入到日志和实例状态。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

终止的实例最终会转换为 `Terminated` 状态。 但是，这种转换不会立即发生。 相反，终止操作将与该实例的其他操作一起排队在任务中心中。 您可以使用[实例查询](#query-instances)api 来了解终止的实例实际何时到达 `Terminated` 状态。

> [!NOTE]
> 当前未传播实例终止。 活动函数和子业务流程运行到完成，而不管您是否终止了调用它们的业务流程实例。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以通过使用[Azure Functions Core Tools](../functions-run-local.md) `durable terminate` 命令，直接终止业务流程实例。 它采用了以下参数：

* **`id` （必需）** ：要终止的业务流程实例的 ID。
* **`reason` （可选）** ：终止的原因。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 还可以使用 durableTask： HubName 在[host json](durable-functions-bindings.md#host-json)中设置它。

以下命令终止 ID 为0ab8c55a66644d68a3a8b220b12d209c 的业务流程实例：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>向实例发送事件

在某些情况下，业务流程协调程序函数可以等待和侦听外部事件，这一点很重要。 这包括[监视函数](durable-functions-overview.md#monitoring)和正在等待[人工交互](durable-functions-overview.md#human)的函数。

使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `RaiseEventAsync` （.net）方法或 `raiseEvent` （JavaScript）方法将事件通知发送到正在运行的实例。 可以处理这些事件的实例是等待调用 `WaitForExternalEvent` （.NET）或生成 `waitForExternalEvent` （JavaScript）调用的实例。

`RaiseEventAsync` （.NET）和 `raiseEvent` （JavaScript）的参数如下所示：

* **InstanceId**：实例的唯一 ID。
* **EventName**：要发送的事件的名称。
* **EventData**：要发送到实例的 JSON 可序列化有效负载。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

> [!NOTE]
> 如果没有具有指定实例 ID 的业务流程实例，则会丢弃事件消息。 如果实例存在但尚未等待事件，则该事件将存储在实例状态中，直到它准备好接收和处理。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以通过使用[Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` 命令，直接将事件引发到业务流程实例。 它采用了以下参数：

* **`id` （必需）** ：业务流程实例的 ID。
* **`event-name`** ：要引发的事件的名称。
* **`event-data`（可选）** ：要发送到业务流程实例的数据。 这可以是 JSON 文件的路径，也可以直接在命令行上提供数据。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 还可以使用 durableTask： HubName 在[host json](durable-functions-bindings.md#host-json)中设置它。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等待业务流程完成

在长时间运行的业务流程中，您可能需要等待并获取业务流程的结果。 在这些情况下，可以在业务流程上定义超时时间也很有用。 如果超出超时，则应返回业务流程的状态而不是结果。

`WaitForCompletionOrCreateCheckStatusResponseAsync` （.NET）或 `waitForCompletionOrCreateCheckStatusResponse` （JavaScript）方法可用于从业务流程实例同步获取实际输出。 默认情况下，这些方法对 `timeout`使用默认值10秒，为 `retryInterval`使用1秒。  

下面的 HTTP 触发型函数示例演示了如何使用此 API：

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

用以下行调用函数。 对于 "重试间隔"，使用2秒的超时时间和0.5 秒：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

根据从业务流程实例获取响应所需的时间，存在两种情况：

* 业务流程实例在定义的超时（在本例中为2秒）内完成，响应是实际的业务流程实例输出，同步传递：

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* 业务流程实例无法在定义的超时内完成，响应是[HTTP API URL 发现](durable-functions-http-api.md)中描述的默认值：

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Webhook Url 的格式可能不同，具体取决于正在运行的 Azure Functions 主机版本。 前面的示例适用于 Azure Functions 2.0 主机。

## <a name="retrieve-http-management-webhook-urls"></a>检索 HTTP 管理 webhook Url

您可以使用外部系统监视或向业务流程引发事件。 外部系统可以通过 webhook Url 与 Durable Functions 进行通信，这些 Url 是[HTTP API URL 发现](durable-functions-http-features.md#http-api-url-discovery)中所述的默认响应的一部分。 此外，可以使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)以编程方式访问 webhook url。 `CreateHttpManagementPayload` （.NET）或 `createHttpManagementPayload` （JavaScript）方法可用于获取包含这些 webhook Url 的可序列化对象。

`CreateHttpManagementPayload` （.NET）和 `createHttpManagementPayload` （JavaScript）方法具有一个参数：

* **instanceId**：实例的唯一 ID。

方法返回具有以下字符串属性的对象：

* **Id**：业务流程的实例 ID（应与 `InstanceId` 输入相同）。
* **StatusQueryGetUri**：业务流程实例的状态 URL。
* **SendEventPostUri**：业务流程实例的“引发事件”URL。
* **TerminatePostUri**：业务流程实例的“终止”URL。
* **PurgeHistoryDeleteUri**：业务流程实例的 "清除历史记录" URL。

函数可以将这些对象的实例发送到外部系统，以监视或引发相应业务流程上的事件，如以下示例中所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableActivityContext` 而不是 `IDurableActivityContext`，而必须使用 `OrchestrationClient` 特性而不是 `DurableClient` 特性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

## <a name="rewind-instances-preview"></a>倒带实例（预览）

如果由于意外原因而导致业务流程发生故障，则可以使用为此目的而构建的 API 将实例*倒带*到以前正常的状态。

> [!NOTE]
> 此 API 不是为了替换正确的错误处理和重试策略。 而是仅用于业务流程实例意外失败的情况。 有关错误处理和重试策略的详细信息，请参阅[错误处理](durable-functions-error-handling.md)文章。

使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `RewindAsync` （.net）或 `rewind` （JavaScript）方法将业务流程重新进入 "*正在运行*" 状态。 此方法还将重新运行导致业务流程失败的活动或子业务流程执行失败。

例如，假设你有一个涉及一系列[人工审批](durable-functions-overview.md#human)的工作流。 假设有一系列活动函数通知某人需要他们的批准，并等待实时响应。 在所有审批活动收到响应或超时后，假设另一个活动因应用程序配置错误（例如无效的数据库连接字符串）而失败。 于是，工作流中存在业务流程故障。 使用 `RewindAsync` （.NET）或 `rewind` （JavaScript） API，应用程序管理员可以修复配置错误，并将失败的业务流程倒带回失败之前的状态。 无需重新批准任何人工交互步骤，并且该业务流程现在可以成功完成。

> [!NOTE]
> *倒带*功能不支持使用持久计时器的倒带业务流程实例。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable rewind` 命令直接倒带业务流程实例。 它采用了以下参数：

* **`id` （必需）** ：业务流程实例的 ID。
* **`reason` （可选）** ：用于倒带实例倒带的原因。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认情况下，使用的是[host. json](durable-functions-bindings.md#host-json)文件中的任务中心名称。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除实例历史记录

若要删除与业务流程相关联的所有数据，可以清除实例历史记录。 例如，你可能想要删除与已完成的实例关联的任何 Azure 表行和大消息 blob。 为此，请使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `PurgeInstanceHistoryAsync` （.net）或 `purgeInstanceHistory` （JavaScript）方法。

此方法有两个重载。 第一次重载按业务流程实例的 ID 清除历史记录：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

请参阅函数 json 配置的[启动实例](#javascript-function-json)。

---

下一个示例显示了一个计时器触发的函数，该函数清除在指定时间间隔后完成的所有业务流程实例的历史记录。 在这种情况下，它会删除30天以前已完成的所有实例的数据。 计划每天运行一次，上午12点：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy` 方法可用于有条件地清除多个实例的实例历史记录。

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> 此示例针对版本 2.x Durable Functions。 在版本1.x 中，使用 `orchestrationClient` 而不是 `durableClient`。

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> 要使清除历史记录操作成功，目标实例的运行时状态必须为 "**已完成**"、"已**终止**" 或 "**失败**"。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` 命令清除业务流程实例的历史记录。 与上一节C#中的第二个示例类似，它清除在指定时间间隔内创建的所有业务流程实例的历史记录。 您可以通过运行时状态进一步筛选已清除的实例。 该命令有多个参数：

* **`created-after`（可选）** ：清除此日期/时间 (UTC) 之后创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）** ：清除此日期/时间 (UTC) 之前创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`runtime-status` （可选）** ：清除具有特定状态的实例的历史记录（例如，"正在运行" 或 "已完成"）。 可以提供多个状态（用空格分隔）。
* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认情况下，使用的是[host. json](durable-functions-bindings.md#host-json)文件中的任务中心名称。

以下命令删除在 7:35 2018 年11月14日（UTC）之前创建的所有失败实例的历史记录。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>删除任务中心

使用[Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` 命令，可以删除与特定任务中心关联的所有存储项目，包括 Azure 存储表、队列和 blob。 该命令有两个参数：

* **`connection-string-setting`（可选）** ：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name` （可选）** ：要使用 Durable Functions 任务中心的名称。 默认情况下，使用的是[host. json](durable-functions-bindings.md#host-json)文件中的任务中心名称。

以下命令将删除与 `UserTest` 任务中心关联的所有 Azure 存储数据。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [用于实例管理的内置 HTTP API 参考](durable-functions-http-api.md)
