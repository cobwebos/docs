---
title: 在 Durable Functions 中管理实例 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中管理实例。
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0e573b4973ea30b990043b54c5cdcf0805135a40
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
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
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
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

> [!NOTE]
> 我们建议为实例 ID 使用随机标识符。 这有助于确保在多个 VM 上缩放业务流程协调程序函数时均衡分配负载。 使用非随机实例 ID 的适当时机是 ID 必须来自外部源，或实现[单一实例业务流程协调程序](durable-functions-singletons.md)模式时。

## <a name="querying-instances"></a>查询实例

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类中的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法查询业务流程实例的状态。 它采用 `instanceId`（必需）、`showHistory`（可选）和 `showHistoryOutput`（可选）作为参数。 如果 `showHistory` 设置为 `true`，则响应将包含执行历史记录。 如果 `showHistoryOutput` 也设置为 `true`，则执行历史记录将包含活动输出。 此方法返回包含以下属性的对象：

* **Name**：业务流程协调程序函数的名称。
* **InstanceId**：业务流程的实例 ID（应与 `instanceId` 输入相同）。
* **CreatedTime**：业务流程协调程序函数开始运行的时间。
* **LastUpdatedTime**：上次创建业务流程检查点的时间。
* **Input**：函数的输入，采用 JSON 值形式。
* **CustomStatus**：JSON 格式的自定义业务流程状态。 
* **Output**：函数的输出，采用 JSON 值形式（如果该函数已完成）。 如果业务流程协调程序函数失败，此属性会包含失败详细信息。 如果业务流程协调程序函数已终止，此属性会包括提供的终止原因（如果有）。
* **RuntimeStatus**：以下值之一：
    * **Running**：实例已开始运行。
    * **Completed**：实例已正常完成。
    * **ContinuedAsNew**：实例已重启自身并生成了新历史记录。 这是暂时性的状态。
    * **Failed**：实例失败并出错。
    * **Terminated**：实例突然终止。
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

## <a name="sending-events-to-instances"></a>将事件发送到实例

可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法将事件通知发送到正在运行的实例。 可以处理这些事件的实例是正在等待调用 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 的实例。 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 的参数如下所示：

* **InstanceId**：实例的唯一 ID。
* **EventName**：要发送的事件的名称。
* **EventData**：要发送到实例的 JSON 可序列化有效负载。

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

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

## <a name="wait-for-orchestration-completion"></a>等待业务流程完成

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类会公开 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API，可以使用该 API 同步获取业务流程实例的实际输出。 该方法使用默认值 10 秒作为 `timeout`，使用 1 秒作为 `retryInterval`（如果未设置这些属性）。  

下面的 HTTP 触发型函数示例演示了如何使用此 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

可以使用以下行调用该函数（使用 2 秒超时和 0.5 秒重试间隔）：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

根据从业务流程实例获取响应所需的时间，存在两种情况：

1. 业务流程实例在定义的超时（此示例中为 2 秒）内完成，响应是同步传送的实际业务流程实例输出：

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

2. 业务流程实例无法在定义的超时（此示例中为 2 秒）内完成，响应是 **HTTP API URL 发现**中所述的默认值：

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
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 前面的示例适用于 Azure Functions 2.0 主机。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 进行实例管理](durable-functions-http-api.md)
