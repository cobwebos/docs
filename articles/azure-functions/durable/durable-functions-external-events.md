---
title: 在 Durable Functions 中处理外部事件 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理外部事件。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7d7fcc725d78b24a93b09cb9c76cf7dc0231cac2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232893"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>在 Durable Functions 中处理外部事件 (Azure Functions)

业务流程协调程序函数能够等待和侦听外部事件。 [Durable Functions](durable-functions-overview.md) 的此功能对于处理人机交互或其他外部触发器通常比较有用。

> [!NOTE]
> 外部事件是单向的异步操作。 它们不适用于发送事件的客户端需要来自业务流程协调程序函数的同步响应的情况。

## <a name="wait-for-events"></a>等待事件

[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)的 `WaitForExternalEvent` （.net）和 `waitForExternalEvent` （JavaScript）方法允许业务流程协调程序函数以异步方式等待和侦听外部事件。 侦听业务流程协调程序函数声明了事件的“名称”和它期望收到的“数据形态”。

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

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
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

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
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> 前面的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

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

`WaitForExternalEvent` 无限期地等待某些输入。  在等待时，可以安全地卸载函数应用。 对于此业务流程实例，如果某个事件到达，则会自动唤醒并立即处理该事件。

> [!NOTE]
> 如果函数应用使用消耗计划，当业务流程协调程序函数等待来自 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) 的任务时，无论它等待多久，都不会产生账单费用。

在 .NET 中，如果事件负载无法转换为预期类型 `T`，将会引发异常。

## <a name="send-events"></a>发送事件

[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)的 `RaiseEventAsync` （.net）或 `raiseEvent` （javascript）方法发送 `WaitForExternalEvent` （.net）或 `waitForExternalEvent` （javascript）等待的事件。  `RaiseEventAsync` 方法采用 *eventName* 和 *eventData* 作为参数。 事件数据必须是 JSON 可序列化的。

下面是一个示例队列触发的函数，它将“Approval”事件发送到一个业务流程协调程序函数实例。 业务流程实例 ID 来自队列消息的正文。

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

在内部，`RaiseEventAsync` (.NET) 或 `raiseEvent` (JavaScript) 将正在等待的业务流程协调程序函数选取的消息排入队列。 如果实例没有在等待指定的事件名，则将事件消息添加到内存中队列。 如果业务流程实例稍后开始侦听该事件名称，它将检查队列中的事件消息。

> [!NOTE]
> 如果没有具有指定*实例 ID* 的业务流程实例，则丢弃事件消息。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何实现错误处理](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [运行等待人机交互的示例](durable-functions-phone-verification.md)