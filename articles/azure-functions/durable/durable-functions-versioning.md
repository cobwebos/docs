---
title: Durable Functions 中的版本控制 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中实现版本控制。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232755"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions 中的版本控制 (Azure Functions)

在应用程序的生存期内添加、删除和更改函数是不可避免的。 [Durable Functions](durable-functions-overview.md) 允许以之前不可能的方式将函数链接在一起，且此链接将影响处理版本控制的方式。

## <a name="how-to-handle-breaking-changes"></a>如何处理重大更改

需要知道一些重大更改示例。 本文介绍最常见的示例。 所有这些示例背后的主题都是，新的和现有的函数业务流程均受函数代码更改所影响。

### <a name="changing-activity-or-entity-function-signatures"></a>Changing activity or entity function signatures

签名更改涉及到函数名称、输入或输出的更改。 If this kind of change is made to an activity or entity function, it could break any orchestrator function that depends on it. 如果更新业务流程协调程序函数以适应此更改，则可能会中断现有的正在进行的实例。

As an example, suppose we have the following orchestrator function.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

此简单函数接收“Foo”结果并将其传递给“Bar”。 假设需要将返回值“Foo”从 `bool` 更改为 `int` 以支持各种不同的结果值。 结果如下所示：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

此更改会应用于业务流程协调程序函数的所有新实例，但会中断任何正在进行的实例。 For example, consider the case where an orchestration instance calls a function named `Foo`, gets back a boolean value, and then checkpoints. 如果此时部署签名更改，则检查点实例在恢复和重播对 `context.CallActivityAsync<int>("Foo")` 的调用时将立即失败。 This failure happens because the result in the history table is `bool` but the new code tries to deserialize it into `int`.

This example is just one of many different ways that a signature change can break existing instances. 一般情况下，如果业务流程协调程序需要更改其调用函数的方式，则此更改可能会出现问题。

### <a name="changing-orchestrator-logic"></a>更改业务流程协调程序逻辑

另一类版本控制问题来自于，以与正在进行的实例的重播逻辑混淆的方式更改业务流程协调程序函数代码。

请考虑以下业务流程协调程序函数：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

现在，假设你想要进行某一项看似无害的更改以添加另一个函数调用。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> The previous C# examples target Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

此更改会向“Foo”和“Bar”之间的“SendNotification”添加新的函数调用。 不存在签名更改。 在从对“Bar”的调用中恢复现有实例时，将出现问题。 During replay, if the original call to **Foo** returned `true`, then the orchestrator replay will call into **SendNotification**, which is not in its execution history. 因此，Durable Task Framework 将失败，并且出现 `NonDeterministicOrchestrationException`，因为在它希望出现对“Bar”的调用时出现了对“SendNotification”的调用。 The same type of problem can occur when adding any calls to "durable" APIs, including `CreateTimer`, `WaitForExternalEvent`, etc.

## <a name="mitigation-strategies"></a>缓解策略

以下是一些用于处理版本控制问题的策略：

* 不执行任何操作
* 停止所有正在进行的实例
* 并行部署

### <a name="do-nothing"></a>不执行任何操作

处理中断更改的最简单方法是使正在进行的业务流程实例失败。 新实例成功运行更改后的代码。

Whether this kind of failure is a problem depends on the importance of your in-flight instances. 如果你处于积极开发状态，且不关心正在进行的实例，那么这可能已足够。 However, you'll need to deal with exceptions and errors in your diagnostics pipeline. 如果想要避免这些事情，请考虑使用其他版本控制选项。

### <a name="stop-all-in-flight-instances"></a>停止所有正在进行的实例

另一个选项是停止所有正在进行的实例。 Stopping all instances can be done by clearing the contents of the internal **control-queue** and **workitem-queue** queues. The instances will be forever stuck where they are, but they will not clutter your logs with failure messages. This approach is ideal in rapid prototype development.

> [!WARNING]
> 这些队列的详细信息可能会随时间而发生更改，因此对于生产工作负载，请不要依赖此方法。

### <a name="side-by-side-deployments"></a>并行部署

确保安全部署中断更改的最万无一失的方法是将其与较旧版本进行并行部署。 可使用以下任何方法来完成此操作：

* Deploy all the updates as entirely new functions, leaving existing functions as-is. This can be tricky because the callers of the new function versions must be updated as well following the same guidelines.
* 将所有更新部署为使用不同存储帐户的新函数应用。
* Deploy a new copy of the function app with the same storage account but with an updated `taskHub` name. Side-by-side deployments is the recommended technique.

### <a name="how-to-change-task-hub-name"></a>如何更改任务中心名称

可在 host.json 文件中配置任务中心，如下所示：

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Functions 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

The default value for Durable Functions v1.x is `DurableFunctionsHub`. Starting in Durable Functions v2.0, the default task hub name is the same as the function app name in Azure, or `TestHubName` if running outside of Azure.

所有 Azure 存储实体都基于 `hubName` 配置值进行命名。 通过给任务中心提供新名称，可以确保为应用程序的新版本创建单独的队列和历史记录表。 The function app, however, will stop processing events for orchestrations or entities created under the previous task hub name.

建议将函数应用的新版本部署到一个新的[部署槽位](../functions-deployment-slots.md)。 通过部署槽位，可以并行运行函数应用的多个副本，且仅其中一个槽位为活动生产槽位。 当准备好向现有基础结构公开新业务流程逻辑时，它可以像将新版本交换到生产槽一样简单。

> [!NOTE]
> 在对业务流程协调程序函数使用 HTTP 和 webhook 触发器时，此策略效果最佳。 For non-HTTP triggers, such as queues or Event Hubs, the trigger definition should [derive from an app setting](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) that gets updated as part of the swap operation.

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理性能和缩放问题](durable-functions-perf-and-scale.md)
