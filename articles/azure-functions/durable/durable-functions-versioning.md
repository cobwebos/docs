---
title: Durable Functions 中的版本控制 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中实现版本控制。
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "74232755"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions 中的版本控制 (Azure Functions)

在应用程序的生存期内添加、删除和更改函数是不可避免的。 [Durable Functions](durable-functions-overview.md) 允许以之前不可能的方式将函数链接在一起，且此链接将影响处理版本控制的方式。

## <a name="how-to-handle-breaking-changes"></a>如何处理重大更改

需要知道一些重大更改示例。 本文介绍最常见的示例。 所有这些示例背后的主题都是，新的和现有的函数业务流程均受函数代码更改所影响。

### <a name="changing-activity-or-entity-function-signatures"></a>更改活动或实体函数签名

签名更改涉及到函数名称、输入或输出的更改。 如果对活动或实体函数进行了此类更改，则可能会中断依赖于它的任何业务流程协调程序函数。 如果更新业务流程协调程序函数以适应此更改，则可能会中断现有的正在进行的实例。

例如，假设有以下业务流程协调程序函数。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

此简单函数接收“Foo”  结果并将其传递给“Bar”  。 假设需要将返回值“Foo”  从 `bool` 更改为 `int` 以支持各种不同的结果值。 结果类似以下形式：

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> 前面的 C# 示例以 Durable Functions 2.x 为目标。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

此更改会应用于业务流程协调程序函数的所有新实例，但会中断任何正在进行的实例。 例如，请考虑这样一种情况：业务流程实例调用名为 `Foo` 的函数并先后返回布尔值和检查点。 如果此时部署签名更改，则检查点实例在恢复和重播对 `context.CallActivityAsync<int>("Foo")` 的调用时将立即失败。 发生此故障是因为历史记录表中的结果为 `bool`，而新代码尝试将其反序列化为 `int`。

这只是签名更改可以中断现有实例的许多不同示例之一。 一般情况下，如果业务流程协调程序需要更改其调用函数的方式，则此更改可能会出现问题。

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
> 前面的 C# 示例以 Durable Functions 2.x 为目标。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

此更改会向“Foo”和“Bar”之间的“SendNotification”添加新的函数调用。 不存在签名更改。 在从对“Bar”  的调用中恢复现有实例时，将出现问题。 在重播过程中，如果对“Foo”  的原始调用返回 `true`，则业务流程协调程序重播将调用不在其执行历史记录中的“SendNotification”  。 因此，Durable Task Framework 将失败，并且出现 `NonDeterministicOrchestrationException`，因为在它希望出现对“Bar”的调用时出现了对“SendNotification”的调用。 添加对“durable”API 的任何调用（包括 `CreateTimer`、`WaitForExternalEvent` 等）时可能会出现相同类型的问题。

## <a name="mitigation-strategies"></a>缓解策略

以下是一些用于处理版本控制问题的策略：

* 不执行任何操作
* 停止所有正在进行的实例
* 并行部署

### <a name="do-nothing"></a>不执行任何操作

处理中断更改的最简单方法是使正在进行的业务流程实例失败。 新实例成功运行更改后的代码。

这种类型的故障是否是一个问题取决于正在进行的实例的重要性。 如果你处于积极开发状态，且不关心正在进行的实例，那么这可能已足够。 但是，你需要处理诊断管道中的异常和错误。 如果想要避免这些事情，请考虑使用其他版本控制选项。

### <a name="stop-all-in-flight-instances"></a>停止所有正在进行的实例

另一个选项是停止所有正在进行的实例。 可通过清除内部“control-queue”  和“workitem-queue”  队列的内容来停止所有实例。 实例将永远停滞在其当前位置，但它们不会导致日志中布满失败消息。 这是适用于快速原型开发的理想方法。

> [!WARNING]
> 这些队列的详细信息可能会随时间而发生更改，因此对于生产工作负载，请不要依赖此方法。

### <a name="side-by-side-deployments"></a>并行部署

确保安全部署中断更改的最万无一失的方法是将其与较旧版本进行并行部署。 可使用以下任何方法来完成此操作：

* 将所有更新部署为全新的函数，保持现有函数不变。 这可能很棘手，因为新函数版本的调用方也必须按照相同的准则进行更新。
* 将所有更新部署为使用不同存储帐户的新函数应用。
* 使用相同的存储帐户但使用更新的 `taskHub` 名称来部署函数应用的新副本。 并行部署是建议的方法。

### <a name="how-to-change-task-hub-name"></a>如何更改任务中心名称

可在 host.json  文件中配置任务中心，如下所示：

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

Durable Functions v1.x 的默认值为 `DurableFunctionsHub`。 从 Durable Functions v2.0 开始，默认任务中心名称与 Azure 中的函数应用名称相同，如果在 Azure 之外运行，则为 `TestHubName`。

所有 Azure 存储实体都基于 `hubName` 配置值进行命名。 通过给任务中心提供新名称，可以确保为应用程序的新版本创建单独的队列和历史记录表。 但是，函数应用将停止处理在前一任务中心名称下创建的业务流程或实体的事件。

建议将函数应用的新版本部署到一个新的[部署槽位](../functions-deployment-slots.md)。 通过部署槽位，可以并行运行函数应用的多个副本，且仅其中一个槽位为活动生产槽位  。 当准备好向现有基础结构公开新业务流程逻辑时，它可以像将新版本交换到生产槽一样简单。

> [!NOTE]
> 在对业务流程协调程序函数使用 HTTP 和 webhook 触发器时，此策略效果最佳。 对于非 HTTP 触发器（如队列或事件中心），触发器定义应[派生自在交换操作过程中更新的应用设置](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理性能和缩放问题](durable-functions-perf-and-scale.md)
