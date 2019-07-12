---
title: Azure Functions 的 Durable Functions 扩展中的函数类型和功能
description: 了解 Azure Functions 的 Durable Functions 业务流程中用于支持函数间通信的函数类型和角色。
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: de5019e0f91c92829082aed962bb9633da52b4a9
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812846"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions 类型和功能 (Azure Functions)

Durable Functions 是 [Azure Functions](../functions-overview.md) 的一个扩展。 可将 Azure Functions 用于函数执行的有状态业务流程。 持久函数是由不同 Azure 函数构成的解决方案。 函数可在持久函数业务流程中扮演不同的角色。 

本文将会概述可在 Durable Functions 业务流程中使用的函数类型。 本文包括一些可用于连接函数的常用模式。 了解 Durable Functions 有助于解决应用开发中遇到的难题。

![显示 Durable Functions 类型的插图][1]  

## <a name="types-of-durable-functions"></a>Durable Functions 的类型

您可以在 Azure Functions 中使用四个持久函数类型： 活动、 业务流程协调程序、 实体和客户端。

### <a name="activity-functions"></a>活动函数

活动函数是持久函数业务流程中的基本工作单元。 活动函数是在过程中协调的函数和任务。 例如，可以创建一个持久函数来处理订单。 任务涉及到检查库存、向客户收费和创建发货单。 每个任务都是一个活动函数。 

活动函数对于可在其中执行的工作类型没有限制。 可以使用 [Durable Functions 支持的任何语言](durable-functions-overview.md#language-support)编写活动函数。 持久任务框架可保证在业务流程期间每个调用的活动函数至少执行一次。

使用[活动触发器](durable-functions-bindings.md#activity-triggers)触发活动函数。 .NET 函数接收 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 作为参数。 还可以将触发器绑定到任何其他对象，从而将输入传递给函数。 在 JavaScript 中，可以通过 [`context.bindings` 对象](../functions-reference-node.md#bindings)中的 `<activity trigger binding name>` 属性访问输入。

活动函数还可以将值返回给业务流程协调程序。 如果从活动函数发送或返回大量的值，可以使用[元组或数组](durable-functions-bindings.md#passing-multiple-parameters)。 只能从业务流程实例触发活动函数。 虽然某个活动函数和另一个函数（例如 HTTP 触发的函数）可以共享一些代码，但每个函数只能有一个触发器。

有关详细信息和示例，请参阅[活动函数](durable-functions-bindings.md#activity-triggers)。

### <a name="orchestrator-functions"></a>业务流程协调程序函数

业务流程协调程序函数描述操作的执行方式和操作的执行顺序。 业务流程协调程序函数描述代码（C# 或 JavaScript）中的业务流程，如 [Durable Functions 模式和技术概念](durable-functions-concepts.md)中所述。 业务流程可以包含许多不同类型的操作，包括[活动函数](#activity-functions)、[子业务流程](#sub-orchestrations)、[等待外部事件](#external-events)和[计时器](#durable-timers)。 业务流程协调程序函数还可以与进行交互[实体函数](#entity-functions)。

必须由[业务流程触发器](durable-functions-bindings.md#orchestration-triggers)触发业务流程协调程序函数。

业务流程协调程序由[业务流程协调程序客户端](#client-functions)启动。 可以从任何源（HTTP、队列、事件流）触发业务流程协调程序。 业务流程的每个实例都有一个实例标识符。 可以自动生成（推荐）或者由用户生成实例标识符。 可以使用实例标识符[管理业务流程的实例](durable-functions-instance-management.md)。

有关详细信息和示例，请参阅[业务流程触发器](durable-functions-bindings.md#orchestration-triggers)。

###  <a name="entity-functions"></a>实体函数 （预览版）

实体函数定义用于读取和更新较小状态片段（称为“持久实体”）的操作。  与业务流程协调程序函数类似，实体函数是具有特殊触发器类型“实体触发器”的函数。  与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

> [!NOTE]
> 实体函数及其相关的功能才可用在 Durable Functions 2.0 及更高版本。

有关实体函数的详细信息，请参阅[实体函数](durable-functions-preview.md#entity-functions)预览功能文档。

### <a name="client-functions"></a>客户端函数

客户端函数是触发的函数的创建和管理业务流程和实体的实例。 它们实际上是 Durable Functions 与交互的入口点。 可以触发一个客户端函数中的任何源 (HTTP、 队列、 事件流，等等)。 客户端函数使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)来创建和管理持久业务流程和实体。

客户端函数的最基本示例是 HTTP 触发的函数，该函数启动业务流程协调程序函数，然后返回检查状态响应。 有关示例，请参阅 [HTTP API URL 发现](durable-functions-http-api.md#http-api-url-discovery)。

有关详细信息和示例，请参阅[业务流程客户端](durable-functions-bindings.md#orchestration-client)。

## <a name="features-and-patterns"></a>功能和模式

后续部分介绍 Durable Functions 类型的功能和模式。

### <a name="sub-orchestrations"></a>子业务流程

业务流程协调程序函数可以调用活动函数，但除此之外，还可以调用其他业务流程协调程序函数。 例如，可以基于业务流程协调程序函数库构建更大的业务流程。 或者，可以并行运行某个业务流程协调程序函数的多个实例。

有关详细信息和示例，请参阅[子业务流程](durable-functions-sub-orchestrations.md)。

### <a name="durable-timers"></a>持久计时器

[Durable Functions](durable-functions-overview.md) 提供持久计时器，在业务流程协调程序函数中使用这些计时器可以针对异步操作实现延迟或设置超时。  在业务流程协调程序函数中应使用持久计时器，而不要使用 `Thread.Sleep` 和 `Task.Delay` (C#) 或 `setTimeout()` 和 `setInterval()` (JavaScript)。

有关详细信息和示例，请参阅[持久计时器](durable-functions-timers.md)。

### <a name="external-events"></a>外部事件

业务流程协调程序函数可以等待外部事件来更新业务流程实例。 此项 Durable Functions 功能通常用于处理人机交互或其他外部回调。

有关详细信息和示例，请参阅[外部事件](durable-functions-external-events.md)。

### <a name="error-handling"></a>错误处理。

使用代码实现 Durable Functions 业务流程。 可以使用编程语言的错误处理功能。 在业务流程中可以运行诸如 `try`/`catch` 的模式。 

Durable Functions 还有内置的重试策略。 发生异常时，某项操作可以自动延迟和重试活动。 可以使用重试来处理暂时性的异常，而无需丢弃业务流程。

有关详细信息和示例，请参阅[错误处理](durable-functions-error-handling.md)。

### <a name="cross-function-app-communication"></a>跨函数应用通信

虽然持久业务流程在单个函数应用的上下文中运行，但你可以使用模式来跨多个函数应用协调业务流程。 跨应用通信可以通过 HTTP 进行，但是，为每个活动使用持久框架意味着还可以跨两个应用保持持久过程。

以下示例演示了 C# 和 JavaScript 中的跨函数应用业务流程。 在每个示例中，有一个活动启动外部业务流程。 另一个活动检索并返回状态。 业务流程协调程序等待状态变为 `Complete`，然后继续。

下面是跨函数应用业务流程的一些示例：

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>后续步骤

若要开始体验，请在 [C#](durable-functions-create-first-csharp.md) 或 [JavaScript](quickstart-js-vscode.md) 中创建第一个持久函数。

> [!div class="nextstepaction"]
> [详细了解 Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
