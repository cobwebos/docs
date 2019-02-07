---
title: 在 Durable Functions 中处理外部事件 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理外部事件。
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7e4b52f0a3ca5e924d9d41e38e51f0cba8b75690
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885807"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>在 Durable Functions 中处理外部事件 (Azure Functions)

业务流程协调程序函数能够等待和侦听外部事件。 [Durable Functions](durable-functions-overview.md) 的此功能对于处理人机交互或其他外部触发器通常比较有用。

> [!NOTE]
> 外部事件是单向的异步操作。 它们不适用于发送事件的客户端需要来自业务流程协调程序函数的同步响应的情况。

## <a name="wait-for-events"></a>等待事件

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 方法允许业务流程协调程序函数以异步方式等待和侦听外部事件。 侦听业务流程协调程序函数声明了事件的“名称”和它期望收到的“数据形态”。

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

前面的示例侦听特定单个事件，并在收到该事件时执行操作。

可以同时侦听多个事件，像以下示例中一样，以下示例等待三个可能的事件通知之一。

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

前面的示例侦听多个事件中的“任何一个”。 还可以等待“所有”事件。

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 无限期地等待一些输入。  在等待时，可以安全地卸载函数应用。 对于此业务流程实例，如果某个事件到达，则会自动唤醒并立即处理该事件。

> [!NOTE]
> 如果函数应用使用消耗计划，当业务流程协调程序函数等待来自 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) 的任务时，无论它等待多久，都不会产生账单费用。

在 .NET 中，如果事件负载无法转换为预期类型 `T`，将会引发异常。

## <a name="send-events"></a>发送事件

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法发送 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) 等待的事件。  `RaiseEventAsync` 方法采用 *eventName* 和 *eventData* 作为参数。 事件数据必须是 JSON 可序列化的。

下面是一个示例队列触发的函数，它将“Approval”事件发送到一个业务流程协调程序函数实例。 业务流程实例 ID 来自队列消息的正文。

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

在内部，`RaiseEventAsync` (.NET) 或 `raiseEvent` (JavaScript) 将正在等待的业务流程协调程序函数选取的消息排入队列。 如果实例没有在等待指定的事件名，则将事件消息添加到内存中队列。 如果业务流程实例稍后开始侦听该事件名称，它将检查队列中的事件消息。

> [!NOTE]
> 如果没有具有指定*实例 ID* 的业务流程实例，则丢弃事件消息。 有关此行为的详细信息，请参阅 [GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues/29)。 

> [!WARNING]
> 在 JavaScript 中进行本地开发时，需将环境变量 `WEBSITE_HOSTNAME` 设置为 `localhost:<port>`（例如， 设置为 `localhost:7071`），以便使用 `DurableOrchestrationClient` 上的方法。 有关此要求的详细信息，请参阅 [GitHub 问题](https://github.com/Azure/azure-functions-durable-js/issues/28)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何设置永久业务流程](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [运行等待外部事件的示例](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [运行等待人机交互的示例](durable-functions-phone-verification.md)