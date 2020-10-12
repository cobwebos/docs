---
title: 在 Durable Functions 中处理外部事件 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中处理外部事件。
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 3cd04c93d508bd06c4ddd2e05074084202b9fc60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87014933"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>在 Durable Functions 中处理外部事件 (Azure Functions)

业务流程协调程序函数能够等待和侦听外部事件。 [Durable Functions](durable-functions-overview.md) 的此功能对于处理人机交互或其他外部触发器通常比较有用。

> [!NOTE]
> 外部事件是单向的异步操作。 它们不适用于发送事件的客户端需要来自业务流程协调程序函数的同步响应的情况。

## <a name="wait-for-events"></a>等待事件

"业务流程触发器绑定" 的 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) ( .net) 、 `waitForExternalEvent` (JavaScript) 和 `wait_for_external_event` (Python) 方法允许 [业务流程](durable-functions-bindings.md#orchestration-trigger) 协调程序函数以异步方式等待和侦听外部事件。 侦听业务流程协调程序函数声明了事件的“名称”和它期望收到的“数据形态”。  

# <a name="c"></a>[C#](#tab/csharp)

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
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

前面的示例侦听特定单个事件，并在收到该事件时执行操作。

可以同时侦听多个事件，像以下示例中一样，以下示例等待三个可能的事件通知之一。

# <a name="c"></a>[C#](#tab/csharp)

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
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

前面的示例侦听多个事件中的“任何一个”。  还可以等待“所有”事件。 

# <a name="c"></a>[C#](#tab/csharp)

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
> 前面的代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

在 .NET 中，如果事件负载无法转换为预期类型 `T`，将会引发异常。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` 无限期地等待一些输入。  在等待时，可以安全地卸载函数应用。 对于此业务流程实例，如果某个事件到达，则会自动唤醒并立即处理该事件。

> [!NOTE]
> 如果函数应用使用消耗计划，则当业务流程协调程序函数等待来自 `WaitForExternalEvent` ( .net) 、 `waitForExternalEvent` (JavaScript) 或 (Python) 的任务时，无论 `wait_for_external_event` 它等待多久，都不会产生计费费用。

## <a name="send-events"></a>发送事件

可以使用 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) 或 `raiseEventAsync` (JavaScript) 方法将外部事件发送到业务流程。 这些方法由[业务流程客户端](durable-functions-bindings.md#orchestration-client)绑定公开。 还可以使用内置[引发事件 HTTP API](durable-functions-http-api.md#raise-event) 将外部事件发送到业务流程。

引发的事件包括实例 ID、eventName 和 eventData 等参数。 业务流程协调程序函数使用 `WaitForExternalEvent` (.NET) 或 `waitForExternalEvent` (JavaScript) API 处理这些事件。 在发送端和接收端，eventName 必须匹配才能处理事件。 事件数据还必须是 JSON 可序列化的。

在内部，“引发事件”机制将正在等待的业务流程协调程序函数选取的消息排入队列。 如果实例没有在等待指定的事件名，则将事件消息添加到内存中队列**。 如果业务流程实例稍后开始侦听该事件名称，** 它将检查队列中的事件消息。

> [!NOTE]
> 如果没有具有指定*实例 ID* 的业务流程实例，则丢弃事件消息。

下面是一个示例队列触发的函数，它将“Approval”事件发送到一个业务流程协调程序函数实例。 业务流程实例 ID 来自队列消息的正文。

# <a name="c"></a>[C#](#tab/csharp)

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
> 前面的 C# 代码适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型而不是 `IDurableOrchestrationClient`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

在内部， `RaiseEventAsync` ( .net) 、 `raiseEvent` (JavaScript) 或 `raise_event` (Python) 排队由等待协调器函数选取的消息。 如果实例没有在等待指定的事件名，则将事件消息添加到内存中队列**。 如果业务流程实例稍后开始侦听该事件名称，** 它将检查队列中的事件消息。

> [!NOTE]
> 如果没有具有指定*实例 ID* 的业务流程实例，则丢弃事件消息。

### <a name="http"></a>HTTP

以下是向业务流程实例引发“批准”事件的 HTTP 请求的示例。 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

在本例中，实例 ID 硬编码为 MyInstanceId。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何实现错误处理](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [运行等待人机交互的示例](durable-functions-phone-verification.md)