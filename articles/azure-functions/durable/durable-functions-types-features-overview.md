---
title: 函数类型和在 Azure Functions 的 Durable Functions 扩展中的功能
description: 了解有关函数和函数函数通信支持在 Azure Functions 的 Durable Functions 业务流程中的角色的类型。
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455728"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable 函数类型和功能 (Azure Functions)

Durable Functions 是的扩展[Azure Functions](../functions-overview.md)。 Durable Functions 可用于有状态业务流程的函数执行。 持久函数是一种解决方案，由不同的 Azure 函数组成。 函数可以在持久函数业务流程中扮演不同角色。 

本文提供了可以使用 Durable Functions 业务流程中的函数的类型的概述。 本文包括了可用于连接的函数的一些常用模式。 了解 Durable Functions 可以帮助您解决您的应用程序开发面临的挑战。

![图像显示类型的持久函数][1]  

## <a name="types-of-durable-functions"></a>Durable Functions 的类型

您可以在 Azure Functions 中使用三个持久函数类型： 活动、 业务流程协调程序，以及客户端。

### <a name="activity-functions"></a>活动函数

活动函数无非是工作的一个持久函数业务流程中的基本单位。 活动函数无非是函数和过程中，来安排的任务。 例如，可能会创建一个持久函数来处理订单。 任务都涉及检查库存、 充电客户，以及创建发货。 每个任务是活动函数。 

可以在其中执行的工作类型的限制不是活动函数。 您可以在任何编写活动函数[Durable Functions 支持的语言](durable-functions-overview.md#language-support)。 持久任务框架可保证在业务流程期间每个调用的活动函数至少执行一次。

使用[活动触发器](durable-functions-bindings.md#activity-triggers)触发的活动函数。 .NET 函数接收[DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)作为参数。 还可以将触发器绑定到任何其他对象，从而将输入传递给函数。 在 JavaScript 中，您可以访问通过输入`<activity trigger binding name>`上的属性[`context.bindings`对象](../functions-reference-node.md#bindings)。

活动函数还可以在业务流程协调程序中返回的值。 如果发送或从活动函数返回大量的值，则可以使用[元组或数组](durable-functions-bindings.md#passing-multiple-parameters)。 可以触发只能从业务流程实例的活动函数。 尽管活动函数和另一个函数 （如 HTTP 触发的函数） 可能会共享一些代码，但每个函数可以具有只有一个触发器。

有关详细信息和示例，请参阅[活动函数](durable-functions-bindings.md#activity-triggers)。

### <a name="orchestrator-functions"></a>业务流程协调程序函数

业务流程协调程序函数用于说明执行操作的方式和操作的执行顺序。 业务流程协调程序函数用于说明代码中的业务流程 (C#或 JavaScript) 中所示[Durable Functions 模式和技术概念](durable-functions-concepts.md)。 业务流程可以有许多不同类型的操作，包括[活动函数](#activity-functions)，[子业务流程](#sub-orchestrations)，[等待外部事件](#external-events)，以及[计时器](#durable-timers)。 

必须由[业务流程触发器](durable-functions-bindings.md#orchestration-triggers)触发业务流程协调程序函数。

通过启动业务流程协调程序[业务流程协调程序客户端](#client-functions)。 可以触发业务流程协调程序从任何源 （HTTP、 队列、 事件流）。 业务流程的每个实例都有实例标识符。 实例标识符可以是自动生成 （推荐） 或用户生成。 可以使用的实例标识符[管理实例](durable-functions-instance-management.md)的业务流程。

有关详细信息和示例，请参阅[业务流程触发器](durable-functions-bindings.md#orchestration-triggers)。

### <a name="client-functions"></a>客户端函数

客户端功能是创建新的业务流程实例的触发的函数。 客户端函数是用于创建 Durable Functions 业务流程实例的入口点。 可以触发一个客户端函数中的任何源 （HTTP、 队列、 事件流）。 可以在任何应用程序支持的语言来编写客户端函数。 

客户端功能还有[业务流程客户端](durable-functions-bindings.md#orchestration-client)绑定。 客户端函数可以使用绑定来创建和管理持久业务流程的业务流程客户端。 

客户端函数的最基本的示例是启动业务流程协调程序函数，然后返回检查状态响应的 HTTP 触发函数。 有关示例，请参阅[HTTP API URL 发现](durable-functions-http-api.md#http-api-url-discovery)。

有关详细信息和示例，请参阅[业务流程客户端](durable-functions-bindings.md#orchestration-client)。

## <a name="features-and-patterns"></a>功能和模式

后续部分介绍的功能和 Durable Functions 类型的模式。

### <a name="sub-orchestrations"></a>子业务流程

业务流程协调程序函数可以调用活动函数，但它们还可以调用其他业务流程协调程序函数。 例如，可以基于业务流程协调程序函数库构建更大的业务流程。 或者，可以并行运行多个业务流程协调程序函数实例。

有关详细信息和示例，请参阅[子业务流程](durable-functions-sub-orchestrations.md)。

### <a name="durable-timers"></a>持久计时器

[Durable Functions](durable-functions-overview.md)提供了*持久计时器*实现延迟或设置上异步操作的超时可以在业务流程协调程序函数中使用。 在业务流程协调程序函数中而不是使用持久计时器`Thread.Sleep`并`Task.Delay`(C#) 或`setTimeout()`并`setInterval()`(JavaScript)。

有关详细信息和示例，请参阅[持久计时器](durable-functions-timers.md)。

### <a name="external-events"></a>外部事件

业务流程协调程序函数可以等待外部事件来更新业务流程实例。 此 Durable Functions 功能通常是适用于处理人机交互或其他外部回调。

有关详细信息和示例，请参阅[外部事件](durable-functions-external-events.md)。

### <a name="error-handling"></a>错误处理。

使用代码来实现 Durable Functions 业务流程。 可以使用编程语言的错误处理功能。 模式喜欢`try` / `catch`工作在业务流程中。 

Durable Functions 还附带了内置重试策略。 操作可以延迟并发生异常时自动重试活动。 重试次数可用于处理暂时性异常，而不放弃该业务流程。

有关详细信息和示例，请参阅[错误处理](durable-functions-error-handling.md)。

### <a name="cross-function-app-communication"></a>跨函数应用通信

尽管持久业务流程在单个函数应用的上下文中运行，但可以使用模式来协调跨多个函数应用的业务流程。 跨应用的通信通过 HTTP，可能会发生，但为每个活动使用持久 framework 意味着您仍然可以在两个应用之间保留一个持久的过程。

以下示例演示了跨函数应用中的业务流程C#和 JavaScript。 在每个示例中，一个活动启动外部业务流程。 另一个活动检索并返回状态。 业务流程协调程序等待状态为`Complete`之前它将继续。

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

若要开始，创建第一个持久函数中的[ C# ](durable-functions-create-first-csharp.md)或[JavaScript](quickstart-js-vscode.md)。

> [!div class="nextstepaction"]
> [了解有关 Durable Functions 的更多信息](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
