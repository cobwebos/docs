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
ms.date: 11/27/2018
ms.author: azfuncdf
ms.openlocfilehash: 9b85ce6bdb7f72c5e4f1cf0d47cc324f5f75ec20
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637262"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>在 Durable Functions 中管理实例 (Azure Functions)

可以启动、终止、查询 [Durable Functions](durable-functions-overview.md) 业务流程实例并向其发送通知事件。 所有实例管理是使用[业务流程客户端绑定](durable-functions-bindings.md)完成的。 本文详细介绍每项实例管理操作。

## <a name="starting-instances"></a>启动实例

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 中的 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 方法启动业务流程协调程序函数的新实例。 可以使用 `orchestrationClient` 绑定获取此类的实例。 在内部，此方法将消息排入控制队列，然后触发具有指定名称的、使用 `orchestrationTrigger` 触发器绑定的函数的启动。 

当业务流程启动时，任务完成。 业务流程应在 30 秒内启动。 如果花费更长时间，会引发 `TimeoutException`。 

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的参数如下所示：

* **Name**：要计划的业务流程协调程序函数的名称。
* **Input**：应作为输入传递给业务流程协调程序函数的任何 JSON 可序列化数据。
* **InstanceId**：（可选）实例的唯一 ID。 如果未指定，将会生成一个随机实例 ID。

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

对于非 .NET 语言，也可以使用函数输出绑定来启动新实例。 在此情况下，可以使用将上述三个参数作为字段的任何 JSON 可序列化对象。 例如，考虑以下 JavaScript 函数：

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

上面的代码假定已在 function.json 文件中定义名为 `starter`、类型为 `orchestrationClient` 的输出绑定。 如果未定义绑定，则不创建持久函数实例。

若要调用持久函数，应将 function.json 修改为具有业务流程客户端绑定，如下所述

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> 为实例 ID 使用随机标识符。 这有助于确保在多个 VM 上缩放业务流程协调程序函数时均衡分配负载。 使用非随机实例 ID 的适当时机是 ID 必须来自外部源，或实现[单一实例业务流程协调程序](durable-functions-singletons.md)模式时。

### <a name="using-core-tools"></a>使用 Core Tools

还可以直接通过 [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` 命令启动实例。 它采用了以下参数：

* **`function-name`（必需）**：要启动的函数的名称
* **`input`（可选）**：以内联方式或通过 JSON 文件提供的函数输入。 对于文件，请使用 `@` 作为文件路径的前缀，例如 `@path/to/file.json`。
* **`id`（可选）**：业务流程实例的 ID。 如果未提供，则会生成随机 GUID。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。 

> [!NOTE]
> Core Tools 命令假设它们要从函数应用的根目录执行。 如果显式提供了 `connection-string-setting` 和 `task-hub-name`，则命令可从任何目录运行。 尽管无需运行函数应用主机即可执行这些命令，但除非运行主机，否则观察不到某些影响。 例如，`start-new` 命令会在目标任务中心内将某个启动消息排队，但除非某个正在运行的函数应用程序主机进程可以处理该消息，否则业务流程实际上不会运行。

以下命令将启动名为 HelloWorld 的函数，并将文件“counter-data.json”的内容传递到该函数：
```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>查询实例

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类中的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法查询业务流程实例的状态。

它采用 `instanceId`（必需）、`showHistory`（可选）、`showHistoryOutput`（可选）和 `showInput`（可选）作为参数。 
* **`showHistory`**：如果设置为 `true`，则响应将包含执行历史记录。 
* **`showHistoryOutput`**：如果设置为 `true`，则执行历史记录将包含活动输出。 
* **`showInput`**：如果设置为 `false`，则响应将不包含函数的输入。 默认值为 `true`。

该方法返回包含以下属性的 JSON 对象：

* **Name**：业务流程协调程序函数的名称。
* **InstanceId**：业务流程的实例 ID（应与 `instanceId` 输入相同）。
* **CreatedTime**：业务流程协调程序函数开始运行的时间。
* **LastUpdatedTime**：上次创建业务流程检查点的时间。
* **Input**：函数的输入，采用 JSON 值形式。 如果 `showInput` 为 false，则不会填充此字段。
* **CustomStatus**：JSON 格式的自定义业务流程状态。 
* **Output**：函数的输出，采用 JSON 值形式（如果该函数已完成）。 如果业务流程协调程序函数失败，此属性会包含失败详细信息。 如果业务流程协调程序函数已终止，此属性会包括提供的终止原因（如果有）。
* **RuntimeStatus**：以下值之一：
    * **挂起**：实例已计划但尚未开始运行。
    * **Running**：实例已开始运行。
    * **Completed**：实例已正常完成。
    * **ContinuedAsNew**：实例已重启自身并生成了新历史记录。 这是暂时性的状态。
    * **Failed**：实例失败并出错。
    * **Terminated**：实例突然停止。
* **History**：业务流程的执行历史记录。 仅当 `showHistory` 设置为 `true` 时，才填充此字段。
    
如果实例不存在或尚未开始运行，此方法会返回 `null`。

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

### <a name="using-core-tools"></a>使用 Core Tools

还可以直接通过 [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` 命令获取业务流程实例的状态。 它采用了以下参数： 

* **`id`（必需）**：业务流程实例的 ID
* **`show-input`（可选）**：如果设置为 `true`，则响应将包含函数的输入。 默认值为 `false`。
* **`show-output`（可选）**：如果设置为 `true`，则响应将包含函数的输出。 默认值为 `false`。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令将检索业务流程实例 ID 为 0ab8c55a66644d68a3a8b220b12d209c 的实例的状态（包括输入和输出）。 假设从函数应用的根目录运行 `func` 命令：
```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` 命令可用于检索业务流程实例的历史记录。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 host.json 中设置此参数。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>查询所有实例

可以使用 `GetStatusAsync` 方法查询所有业务流程实例的状态。 它不带任何参数，如果要取消该查询，可以传递 `CancellationToken` 对象。 该方法返回具有与带参数的 `GetStatusAsync` 方法相同属性的对象，但它不返回历史记录。 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="using-core-tools"></a>使用 Core Tools

还可以直接通过 [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` 命令查询实例。 它采用了以下参数：

* **`top`（可选）**：此命令支持分页。 此参数对应于每个请求检索的实例数。 默认值为 10。
* **`continuation-token`（可选）**：用于指示要检索的实例页/节的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>使用筛选器查询实例

此外，还可以使用 `GetStatusAsync` 方法获取匹配一组预定义筛选器的业务流程实例的列表。 可能的筛选器选项包括业务流程创建时间和业务流程运行时状态。

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

### <a name="using-the-functions-core-tools"></a>使用 Functions Core Tools

还可以结合筛选器使用 `durable get-instances` 命令。 除了前面提到的 `top`、`continuation-token`、`connection-string-setting` 和 `task-hub-name` 参数以外，还可以使用三个筛选器参数（`created-after`、`created-before` 和 `runtime-status`）。 

* **`created-after`（可选）**：检索在此日期/时间 (UTC) 之后创建的实例。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）**：检索在此日期/时间 (UTC) 之前创建的实例。 接受 ISO 8601 格式的日期时间。
* **`runtime-status`（可选）**：检索其状态与这些值（“running”、“completed”等）匹配的实例。 可以提供多个状态（用空格分隔）。
* **`top`（可选）**：每个请求检索的实例数。 默认值为 10。
* **`continuation-token`（可选）**：用于指示要检索的实例页/节的标记。 每次执行 `get-instances` 都会向下一个实例集返回一个标记。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

如果未提供筛选器（`created-after`、`created-before` 或 `runtime-status`），则无论运行时状态或创建时间是什么，都会检索 `top` 实例。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>终止实例

可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类的 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 方法终止正在运行的业务流程实例。 两个参数为 `instanceId` 和 `reason` 字符串，将写入日志和实例状态。 终止的实例在达到下一个 `await` 点时会立即停止运行，或者已进入 `await` 点时会立即终止。 

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

> [!NOTE]
> 当前不传播实例终止。 无论调用活动函数和子业务流程的业务流程实例是否已终止，活动函数和子业务流程都将运行至完成状态。

### <a name="using-core-tools"></a>使用 Core Tools

还可以直接通过 [Core Tools](../functions-run-local.md) `durable terminate` 命令终止业务流程实例。 它采用了以下参数：

* **`id`（必需）**：要终止的业务流程实例的 ID
* **`reason`（可选）**：终止的原因
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令将终止 ID 为 0ab8c55a66644d68a3a8b220b12d209c 的业务流程实例：
```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>将事件发送到实例

可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法将事件通知发送到正在运行的实例。 可以处理这些事件的实例是正在等待调用 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 的实例。 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 的参数如下所示：

* **InstanceId**：实例的唯一 ID。
* **EventName**：要发送的事件的名称。
* **EventData**：要发送到实例的 JSON 可序列化有效负载。

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

> [!WARNING]
> 如果不存在具有指定实例 ID 的业务流程实例，或者该实例不在等待指定的事件名称，则会丢弃事件消息。 有关此行为的详细信息，请参阅 [GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

### <a name="using-core-tools"></a>使用 Core Tools

还可以直接通过 [Core Tools](../functions-run-local.md) `durable raise-event` 命令向业务流程实例引发事件。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID
* **`event-name`（可选）**：要引发的事件的名称。 默认值为 `$"Event_{RandomGUID}"`
* **`event-data`（可选）**：要发送到业务流程实例的数据。 这可以是某个 JSON 文件的路径，或者，可以直接在命令行中提供数据
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```
```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等待业务流程完成

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类会公开 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API，可以使用该 API 同步获取业务流程实例的实际输出。 该方法使用默认值 10 秒作为 `timeout`，使用 1 秒作为 `retryInterval`（如果未设置这些属性）。  

下面的 HTTP 触发型函数示例演示了如何使用此 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

可以使用以下行调用该函数（使用 2 秒超时和 0.5 秒重试间隔）：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

根据从业务流程实例获取响应所需的时间，存在两种情况：

* 业务流程实例在定义的超时（此示例中为 2 秒）内完成，响应是同步传送的实际业务流程实例输出：

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

* 业务流程实例无法在定义的超时（此示例中为 2 秒）内完成，响应是 **HTTP API URL 发现**中所述的默认值：

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
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 前面的示例适用于 Azure Functions 2.0 主机。

## <a name="retrieving-http-management-webhook-urls"></a>检索 HTTP 管理 Webhook URL

外部系统可以通过作为 [HTTP API URL 发现](durable-functions-http-api.md)中所述的默认响应一部分的 Webhook URL 与 Durable Functions 进行通信。 但是，Webhook URL 也可以在业务流程客户端中以编程方式进行访问，或者在活动函数中通过 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类的 [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 方法进行访问。 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 具有一个参数：

* **instanceId**：实例的唯一 ID。

该方法返回 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) 的一个实例，该实例具有以下字符串属性：

* **Id**：业务流程的实例 ID（应与 `InstanceId` 输入相同）。
* **StatusQueryGetUri**：业务流程实例的状态 URL。
* **SendEventPostUri**：业务流程实例的“引发事件”URL。
* **TerminatePostUri**：业务流程实例的“终止”URL。
* **RewindPostUri**：业务流程实例的“回退”URL。

活动函数可以将 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) 的实例发送到外部系统，以监视或引发到业务流程的事件：

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

## <a name="rewinding-instances-preview"></a>回退实例（预览）

可以使用 [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) API 将失败的业务流程实例回退到以前的正常状态。 工作原理是将业务流程恢复到“正在运行”状态并重新运行导致业务流程失败的活动和/或子业务流程执行失败。

> [!NOTE]
> 此 API 不是为了替换正确的错误处理和重试策略。 而是仅用于业务流程实例意外失败的情况。 有关错误处理和重试策略的更多详细信息，请参阅[错误处理](durable-functions-error-handling.md)主题。

回退用例的一个示例是涉及一系列[人工审批](durable-functions-overview.md#pattern-5-human-interaction)的工作流。 假设有一系列活动函数，用于通知某人需要其审批并等待实时响应。 在所有审批活动收到响应或超时后，另一活动因应用程序配置错误（例如数据库连接字符串无效）而失败。 于是，工作流中存在业务流程故障。 使用 `RewindAsync` API，应用程序管理员可以修复配置错误并将失败的业务流程回退到失败前的状态。 无需再次审批任何人工交互步骤，业务流程现可成功完成。

> [!NOTE]
> 回退功能不支持回退使用持久计数器的业务流程实例。

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

### <a name="using-core-tools"></a>使用 Core Tools

还可以直接通过 [Core Tools](../functions-run-local.md) `durable rewind` 命令回退业务流程实例。 它采用了以下参数：

* **`id`（必需）**：业务流程实例的 ID
* **`reason`（可选）**：回退业务流程实例的原因
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除实例历史记录

可以使用 [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) 清除业务流程历史记录。 该功能将删除与业务流程关联的所有数据 - Azure 表行，以及大消息 Blob（如果存在）。 该方法有两个重载。 第一个示例按业务流程实例的 ID 清除历史记录：

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

第二个示例演示一个计时器触发的函数，该函数清除在指定的时间间隔后完成的所有业务流程实例的历史记录。 在本例中，该函数将删除 30 天或更长时间以前完成的所有实例的数据。 该函数计划为在每天的凌晨 12 点运行一次：

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.InnerClient.PurgeInstanceHistoryAsync( 
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus> 
                    { 
                        OrchestrationStatus.Completed
                    }); 
}
```

> [!NOTE]
> 接受使用时间段作为参数的 *PurgeInstanceHistory* 重载只处理处于以下运行时状态之一的业务流程实例：Completed、Terminated 或 Failed。

### <a name="using-core-tools"></a>使用 Core Tools

可以使用 [Core Tools](../functions-run-local.md) `durable purge-history` 命令清除业务流程实例的历史记录。 类似于上面的第二个 C# 示例，该命令将清除在指定时间间隔内创建的所有业务流程实例的历史记录。 可按运行时状态进一步筛选清除的实例。 该命令有多个参数：

* **`created-after`（可选）**：清除此日期/时间 (UTC) 之后创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`created-before`（可选）**：清除此日期/时间 (UTC) 之前创建的实例的历史记录。 接受 ISO 8601 格式的日期时间。
* **`runtime-status`（可选）**：清除其状态与这些值（“running”、“completed”等）匹配的实例的历史记录。 可以提供多个状态（用空格分隔）。
* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令将删除在 2018 年 11 月 14 日下午 7:35 (UTC) 之前创建的所有失败实例的历史记录。
```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>删除任务中心
使用 [Core Tools](../functions-run-local.md) `durable delete-task-hub` 命令可以删除与特定任务中心关联的所有存储项目。 这包括 Azure 存储表、队列和 Blob。 该命令有两个参数： 

* **`connection-string-setting`（可选）**：包含要使用的存储连接字符串的应用程序设置的名称。 默认值为 AzureWebJobsStorage。
* **`task-hub-name`（可选）**：要使用的持久任务中心的名称。 默认值为 DurableFunctionsHub。 也可以通过 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令将删除与“UserTest”任务中心关联的所有 Azure 存储数据。
```bash
func durable delete-task-hub --task-hub-name UserTest
```


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 进行实例管理](durable-functions-http-api.md)
