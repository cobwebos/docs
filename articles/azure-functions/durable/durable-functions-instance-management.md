---
title: 在 Durable Functions 中管理实例 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中管理实例。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547301"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的实例

如果您使用的[Durable Functions](durable-functions-overview.md)扩展 Azure Functions 或想要开始执行此操作，请确保您将获得最佳使用带它。 可以通过学习有关如何管理它们的详细信息来优化您的 Durable Functions 业务流程实例。 本文详细介绍每项实例管理操作。

可以启动和终止的实例，例如，同时还可以查询实例，包括与筛选器查询的所有实例和都查询实例的功能。 此外，您可以将事件发送到实例，等待业务流程完成时，并检索 HTTP 管理 webhook Url。 这篇文章介绍其他管理操作，也包括后退实例、 清除实例历史记录和删除任务中心。

在 Durable Functions，可以为你想要实现这些管理操作的每个选项。 本文提供使用的示例[Azure Functions Core Tools](../functions-run-local.md)适用于这两个.NET (C#) 和 JavaScript。

## <a name="start-instances"></a>启动实例

请务必将无法启动业务流程的实例。 这通常是出现在另一个函数的触发器中使用 Durable Functions 绑定时。

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) 或`startNew`上`DurableOrchestrationClient`(JavaScript) 启动的新实例。 通过获取此类的实例`orchestrationClient`绑定。 在内部，此方法将消息排入控制队列，然后触发具有指定名称的、使用 `orchestrationTrigger` 触发器绑定的函数的启动。

当业务流程成功计划时，此异步操作完成。 业务流程应在 30 秒内启动。 如果它花费的时间，则会看到`TimeoutException`。

> [!WARNING]
> 本地开发时在 JavaScript 中，设置环境变量`WEBSITE_HOSTNAME`到`localhost:<port>`(例如， `localhost:7071`) 上使用方法`DurableOrchestrationClient`。 有关此要求的详细信息，请参阅 [GitHub 问题](https://github.com/Azure/azure-functions-durable-js/issues/28)。

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的参数如下所示：

* **名称**：要计划的业务流程协调程序函数的名称。
* **输入**：应作为输入传递给业务流程协调程序函数的任何 JSON 可序列化数据。
* **InstanceId**：（可选）实例的唯一 ID。 如果未指定此参数，该方法使用一个随机的 id。

下面是一个简单的 C# 示例：

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

`startNew` 的参数如下：

* **名称**：要计划的业务流程协调程序函数的名称。
* **InstanceId**：（可选）实例的唯一 ID。 如果未指定此参数，该方法使用一个随机的 id。
* **输入**：（可选）应作为输入传递给业务流程协调程序函数的任何 JSON 可序列化数据。

下面是一个简单的 JavaScript 示例：

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> 为实例 ID 使用随机标识符。 这有助于确保你正在跨多个 Vm 扩展业务流程协调程序函数时均衡分配负载。 使用非随机实例 Id 的适当时机是 ID 必须来自外部源，或您在实施[单一实例业务流程协调程序](durable-functions-singletons.md)模式。

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

您还可以直接通过使用启动实例[Azure Functions Core Tools](../functions-run-local.md) `durable start-new`命令。 它采用了以下参数：

* **`function-name`（必需）**：要启动的函数的名称。
* **`input`（可选）**：输入到函数，以内联方式或通过 JSON 文件。 对于文件，将前缀添加到与文件的路径`@`，如`@path/to/file.json`。
* **`id`（可选）**：业务流程实例的 ID。 如果未指定此参数，该命令使用的随机 GUID。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认值为 DurableFunctionsHub。 您还可以在设置这[host.json](durable-functions-bindings.md#host-json)使用 durableTask:HubName。

> [!NOTE]
> 核心工具命令假定您正在从 function app 的根目录运行它们。 如果显式提供`connection-string-setting`和`task-hub-name`参数，您可以从任何目录运行命令。 尽管可以不运行的函数应用程序主机的情况下运行这些命令，但可能会发现，除非该主机正在运行，无法观察一些效果。 例如，`start-new`命令排入队列函数应用程序主机进程运行，除非实际上不会运行一条启动消息到目标任务中心，但业务流程可以处理该消息。

以下命令启动名为 HelloWorld，该函数，并将传递该文件的内容`counter-data.json`到它：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查询实例

作为您的工作来管理您的业务流程的一部分，很可能需要收集状态信息的业务流程实例 （例如，无论它已正常完成或失败）。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类中的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法 (.NET) 或 `DurableOrchestrationClient` 类中的 `getStatus` 方法 (JavaScript) 查询业务流程实例的状态。

它采用 `instanceId`（必需）、`showHistory`（可选）、`showHistoryOutput`（可选）和 `showInput`（可选，仅 .NET）作为参数。

* **`showHistory`**：如果设置为`true`，响应中包含的执行历史记录。
* **`showHistoryOutput`**：如果设置为`true`，执行历史记录包含活动输出。
* **`showInput`**：如果设置为`false`，响应将不包含该函数的输入。 默认值为 `true`。 （仅 .NET）

该方法返回包含以下属性的 JSON 对象：

* **名称**：业务流程协调程序函数的名称。
* **InstanceId**：业务流程的实例 ID（应与 `instanceId` 输入相同）。
* **CreatedTime**：业务流程协调程序函数开始运行的时间。
* **LastUpdatedTime**：上次创建业务流程检查点的时间。
* **输入**：函数的输入，采用 JSON 值形式。 如果不填充此字段`showInput`为 false。
* **CustomStatus**：JSON 格式的自定义业务流程状态。
* **输出**：函数的输出，采用 JSON 值形式（如果该函数已完成）。 如果业务流程协调程序函数失败，此属性将包含失败详细信息。 如果业务流程协调程序函数已终止，此属性包含为终止原因 （如果有）。
* **RuntimeStatus**：以下值之一：
  * **Pending**：实例已计划但尚未开始运行。
  * **Running**：实例已开始运行。
  * **Completed**：实例已正常完成。
  * **ContinuedAsNew**：实例已重启自身并生成了新历史记录。 这是暂时性的状态。
  * **Failed**：实例失败并出错。
  * **Terminated**：实例突然停止。
* **History**：业务流程的执行历史记录。 仅当 `showHistory` 设置为 `true` 时，才填充此字段。

如果实例不存在或尚未开始运行，此方法会返回 `null`。

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

还有可能要使用直接获取业务流程实例的状态[Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`命令。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID。
* **`show-input`（可选）**：如果设置为`true`，响应包含该函数的输入。 默认值为 `false`。
* **`show-output`（可选）**：如果设置为`true`，响应包含函数的输出。 默认值为 `false`。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

以下命令将检索 0ab8c55a66644d68a3a8b220b12d209c 的业务流程实例 ID 与实例的状态 （包括输入和输出）。 它假定您正在运行`func`function app 的根目录中的命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

可以使用`durable get-history`命令来检索业务流程实例的历史记录。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 它还可以将设置在 host.json，通过使用 durableTask:HubName。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查询所有实例

而不是查询一次在业务流程中的一个实例，您可能会发现它们全部同时查询效率更高。

可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法查询所有业务流程实例的状态。 在.NET 中，可以传递`CancellationToken`对象以防你想要取消它。 该方法返回具有与带参数的 `GetStatusAsync` 方法相同属性的对象。

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

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

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

它还通过就可以直接查询实例使用[Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`命令。 它采用了以下参数：

* **`top`（可选）**：此命令支持分页。 此参数对应于每个请求检索的实例数。 默认值为 10。
* **`continuation-token`（可选）**：用于指示的页面或要检索的实例的部分的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>使用筛选器的查询实例

如果您不真正需要所有的标准实例查询可以提供的信息？ 例如，如果你只希望为业务流程创建时间或业务流程运行时状态？ 可以通过应用筛选器缩小您的查询。

使用`GetStatusAsync`(.NET) 或`getStatusBy`(JavaScript) 方法以获取一系列业务流程实例的一组匹配的预定义的筛选器。

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

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

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

在 Azure Functions Core Tools，您还可以使用`durable get-instances`命令和筛选器。 除了前面提到`top`， `continuation-token`， `connection-string-setting`，和`task-hub-name`参数，可以使用三个筛选器参数 (`created-after`， `created-before`，和`runtime-status`)。

* **`created-after`（可选）**：检索在此日期/时间 (UTC) 之后创建的实例。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）**：检索在此日期/时间 (UTC) 之前创建的实例。 接受 ISO 8601 格式的日期时间。
* **`runtime-status`（可选）**：检索具有特定状态 （例如，在运行或已完成） 的实例。 可以提供多个状态（用空格分隔）。
* **`top`（可选）**：每个请求检索的实例数。 默认值为 10。
* **`continuation-token`（可选）**：用于指示的页面或要检索的实例的部分的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

如果未提供任何筛选器 (`created-after`， `created-before`，或`runtime-status`)，该命令就只检索`top`实例，而不考虑到运行时状态或创建时间。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>终止实例

如果有时间太长，若要运行，业务流程实例或只需出于任何原因完成前停止它，可以选择以终止它。

可以使用[TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)类 (.NET) 或`terminate`方法`DurableOrchestrationClient`类 (JavaScript)。 两个参数是`instanceId`和一个`reason`字符串，将写入日志和实例状态。 终止的实例停止运行一旦达到下一步`await`(.NET) 或`yield`(JavaScript) 点，或它会立即终止如果已在上`await`或`yield`。

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> 当前不传播实例终止。 活动函数和子业务流程运行完成，而不考虑是否已终止调用它们的业务流程实例。

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

也可以终止业务流程实例直接通过[Azure Functions Core Tools](../functions-run-local.md) `durable terminate`命令。 它采用了以下参数：

* **`id`（必需）**：要终止的业务流程实例的 ID。
* **`reason`（可选）**：终止的原因。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

以下命令终止 0ab8c55a66644d68a3a8b220b12d209c 业务流程的实例 id:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>将事件发送到实例

在某些情况下，非常重要的业务流程协调程序函数能够等待和侦听外部事件。 这包括[监视 functions](durable-functions-concepts.md#monitoring)和函数正在等待[人机交互](durable-functions-concepts.md#human)。

将事件通知发送到正在运行的实例使用[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)类 (.NET) 或`raiseEvent`方法`DurableOrchestrationClient`类 （JavaScript)。 可以处理这些事件的实例是正在等待调用 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) 或 `waitForExternalEvent` (JavaScript) 的实例。

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) 和 `raiseEvent` (JavaScript) 的参数如下所示：

* **InstanceId**：实例的唯一 ID。
* **EventName**：要发送的事件的名称。
* **EventData**：要发送到实例的 JSON 可序列化有效负载。

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> 如果没有业务流程实例与指定的实例 ID，或者该实例不在等待指定的事件名称，则丢弃事件消息。 有关此行为的详细信息，请参阅 [GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

您也可以引发事件到业务流程实例直接，通过使用[Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`命令。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID。
* **`event-name`（可选）**：要引发的事件的名称。 默认为 `$"Event_{RandomGUID}"`。
* **`event-data`（可选）**：要发送到业务流程实例的数据。 这可以是 JSON 文件的路径，也可以直接在命令行上提供的数据。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等待业务流程完成

在长时间运行的业务流程，你可能想要等待和获取业务流程的结果。 在这些情况下，也很有用，因为可以定义业务流程上的超时期限。 如果超时，业务流程的状态应返回而不是结果。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)类公开[WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_)在.NET 中的 API。 此 API 可用于以同步方式从业务流程实例获取的实际输出。 在 JavaScript 中，`DurableOrchestrationClient` 类为了同一目的公开 `waitForCompletionOrCreateCheckStatusResponse` API。 这些方法时未设置，使用默认值为 10 秒钟，让`timeout`，并为 1 秒`retryInterval`。  

下面的 HTTP 触发型函数示例演示了如何使用此 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

调用具有以下行的函数。 使用重试时间间隔 2 秒的超时和 0.5 秒：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

根据从业务流程实例获取响应所需的时间，存在两种情况：

* （在此示例 2 秒） 中定义的超时内完成的业务流程实例和响应是同步传送的实际的业务流程实例输出：

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* 业务流程实例中定义的超时，无法完成并且响应是一个中所述的默认[HTTP API URL 发现](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Webhook Url 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 前面的示例适用于 Azure Functions 2.x 主机。

## <a name="retrieve-http-management-webhook-urls"></a>检索 HTTP 管理 webhook Url

可以使用外部系统来监视或将事件引发到业务流程。 外部系统可与其 Durable Functions 通过 webhook Url 的一部分中所述的默认响应[HTTP API URL 发现](durable-functions-http-api.md)。 但是，webhook Url 还可以访问以编程方式在业务流程客户端或活动函数中。 执行此操作通过使用[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)类 (.NET) 或`createHttpManagementPayload`方法`DurableOrchestrationClient`类 (JavaScript)。

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 和 `createHttpManagementPayload` 有一个参数：

* **instanceId**：实例的唯一 ID。

方法返回的实例[HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) 或具有以下字符串属性的对象 (JavaScript):

* **Id**：业务流程的实例 ID（应与 `InstanceId` 输入相同）。
* **StatusQueryGetUri**：业务流程实例的状态 URL。
* **SendEventPostUri**：业务流程实例的“引发事件”URL。
* **TerminatePostUri**：业务流程实例的“终止”URL。
* **RewindPostUri**：业务流程实例的“回退”URL。

活动函数可以将这些对象的一个实例发送到外部系统，以监视或引发到业务流程的事件：

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
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

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

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

## <a name="rewind-instances-preview"></a>快退实例 （预览版）

如果由于意外原因有业务流程失败，则可以*rewind*通过使用 API 之前恢复正常状态的实例生成实现该目的。

> [!NOTE]
> 此 API 不是为了替换正确的错误处理和重试策略。 而是仅用于业务流程实例意外失败的情况。 错误处理和重试策略的详细信息，请参阅[错误处理](durable-functions-error-handling.md)主题。

使用[RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) 或`rewindAsync`(JavaScript) API 以将放在业务流程回*运行*状态。 重新运行导致业务流程失败的活动或 suborchestration 执行失败。

例如，假设您的工作流涉及一系列[人工批准](durable-functions-concepts.md#human)。 假设有一系列的人，其审批所需要的以及查看实时响应等待通知的活动函数。 毕竟的审批活动已收到响应或超时，假设另一个活动因应用程序配置错误，如无效的数据库连接字符串。 于是，工作流中存在业务流程故障。 与`RewindAsync`(.NET) 或`rewindAsync`(JavaScript) API，应用程序管理员可以修复配置错误，并进行倒带恢复到在发生故障之前立即状态失败的业务流程。 无用户交互步骤需要重新获得批准，并且该业务流程现在可以成功完成。

> [!NOTE]
> *Rewind*功能尚不支持使用持久计时器的倒带业务流程实例。

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

您还可以直接通过使用 rewind 业务流程实例[Azure Functions Core Tools](../functions-run-local.md) `durable rewind`命令。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID。
* **`reason`（可选）**：后退业务流程实例的原因。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除实例历史记录

若要删除与业务流程相关联的所有数据，可以清除实例历史记录。 例如，你可能想要删除的 Azure 表行和大消息 blob，如果它们存在。 若要执行此操作，请使用[PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API。

> [!NOTE]
> `PurgeInstanceHistoryAsync` API 目前仅适用于 C#。

 该方法有两个重载。 第一个清除历史记录由业务流程实例 ID:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

第二个示例演示一个计时器触发的函数，该函数清除在指定的时间间隔后完成的所有业务流程实例的历史记录。 在这种情况下，它将删除所有实例都已完成 30 个或更多天前的数据。 计划每天运行一次，上午 12:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
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
> 若要成功执行的时间触发的函数进程，必须是运行时状态**已完成**， **Terminated**，或**失败**。

### <a name="azure-functions-core-tools"></a>Azure Functions 核心工具

可以通过清除业务流程实例的历史记录[Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`命令。 类似于第二个C#一节中的示例中，它会清除指定的时间间隔内创建的所有业务流程实例的历史记录。 按运行时状态，可以进一步筛选已清除的实例。 该命令有多个参数：

* **`created-after`（可选）**：清除此日期/时间 (UTC) 之后创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）**：清除此日期/时间 (UTC) 之前创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`runtime-status`（可选）**：清除具有特定状态 （例如，在运行或已完成） 的实例的历史记录。 可以提供多个状态（用空格分隔）。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

以下命令将删除所有失败的实例在 7:35 PM (UTC) 在 2018 年 11 月 14 日之前创建的历史记录。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>删除任务中心

使用[Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub`命令时，可以删除与特定任务中心关联的所有存储项目。 这包括 Azure 存储表、队列和 Blob。 该命令有两个参数：

* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认为 `AzureWebJobsStorage`。
* **`task-hub-name`（可选）**：若要使用的 Durable Functions 任务中心的名称。 默认为 `DurableFunctionsHub`。 此外可以设置[host.json](durable-functions-bindings.md#host-json)，通过使用 durableTask:HubName。

以下命令将删除与相关联的所有 Azure 存储数据`UserTest`任务中心。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 进行实例管理](durable-functions-http-api.md)
