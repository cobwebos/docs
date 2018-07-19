---
title: Durable Functions 的函数类型和功能概述 - Azure
description: 了解允许作为 Durable Function 业务流程的一部分进行函数到函数通信的函数类型和角色。
services: functions
author: jeffhollan
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: dfcf52c6d8d5c04e15d653376f52107fc4776d02
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973149"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Durable Functions 的函数类型和功能概述 (Azure Functions)

Azure Durable Functions 提供函数执行的状态性业务流程。  Durable Function 是由不同 Azure Functions 组成的解决方案。  每一个函数都可以在业务流程中扮演不同的角色。  以下文档概述了 Durable Function 业务流程中涉及的函数类型。  还介绍了一些将函数连接在一起的常见模式。  

![Durable Functions 的类型][1]  

## <a name="types-of-functions"></a>函数类型

### <a name="activity-functions"></a>活动函数

活动函数是持久业务流程中的基本工作单元。  活动函数是流程中安排的函数和任务。  例如，可以创建一个 Durable Function 来处理订单，这包括检查库存、向客户收费和创建装运作业。  以上每一个任务都是一个活动函数。  活动函数对于可在其中执行的工作类型没有任何限制。  可以使用 Azure Functions 支持的任何语言编写这类函数。  持久任务框架可保证在业务流程期间每个调用的活动函数至少执行一次。

活动函数必须由[活动触发器](durable-functions-bindings.md#activity-triggers)触发。  此函数将接收 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 作为参数。 还可以将触发器绑定到任何其他对象，从而将输入传递给函数。  活动函数还可以将值返回到业务流程协调程序。  如果从活动函数发送或返回多个值，则可以[利用元组或数组](durable-functions-bindings.md#passing-multiple-parameters)。  只能从业务流程实例触发活动函数。  虽然某些代码可以在活动函数和另一个函数（如 HTTP 触发器函数）之间共享，但每个函数只能有一个触发器。

可参阅 [Durable Functions 绑定文章](durable-functions-bindings.md#activity-triggers)了解更多信息和示例。

### <a name="orchestrator-functions"></a>业务流程协调程序函数

业务流程协调程序函数是 Durable Function 的核心。  业务流程协调程序函数描述执行操作的方式和顺序。  业务流程协调程序函数描述代码（C# 或 JavaScript）中的业务流程，如[s 概述](durable-functions-overview.md)中所示。  业务流程可以有许多不同类型的操作，例如[活动函数](#activity-functions)、[子业务流程](#sub-orchestrations)、[等待外部事件](#external-events)和[计时器](#durable-timers)。  

必须由[业务流程触发器](durable-functions-bindings.md#orchestration-triggers)触发业务流程协调程序函数。

业务流程协调程序由[业务流程协调程序客户端](#client-functions)启动，该客户端本身可以从任何源（HTTP、队列、事件流）触发。  业务流程的每个实例都有一个实例标识符，该标识符可以自动生成（推荐）或用户生成。  此标识符可用于[管理业务流程的实例](durable-functions-instance-management.md)。

可参阅 [Durable Functions 绑定文章](durable-functions-bindings.md#orchestration-triggers)了解更多信息和示例。

### <a name="client-functions"></a>客户端函数

客户端函数是触发器函数，可创建新的业务流程实例。  它们是创建持久业务流程实例的入口点。  客户端函数可以由任何触发器（HTTP、队列、事件流等）触发，并能以应用支持的任何语言编写。  除了触发器之外，客户端函数还具有[业务流程客户端](durable-functions-bindings.md#orchestration-client)绑定，允许其创建和管理持久业务流程。  客户端函数的最基本示例是 HTTP 触发器函数，该函数启动业务流程协调程序函数并返回检查状态响应，如[以下示例所示](durable-functions-http-api.md#http-api-url-discovery)。

可参阅 [Durable Functions 绑定文章](durable-functions-bindings.md#orchestration-client)了解更多信息和示例。

## <a name="features-and-patterns"></a>功能和模式

### <a name="sub-orchestrations"></a>子业务流程

除了调用活动函数之外，业务流程协调程序函数还可以调用其他业务流程协调程序函数。 例如，可以基于业务流程协调程序函数库构建更大的业务流程。 或者，你可以并行运行某个业务流程协调程序函数的多个实例。

可参阅[子业务流程文章](durable-functions-sub-orchestrations.md)了解更多信息和示例。

### <a name="durable-timers"></a>持久计时器

[Durable Functions](durable-functions-overview.md) 提供了供在业务流程协调程序函数中使用的“持久计时器”，这些计时器用来为异步操作实现延迟或设置超时。 在业务流程协调程序函数中应当使用持久计时器，而不是使用 `Thread.Sleep` 和 `Task.Delay` (C#) 或 `setTimeout()` 和 `setInterval()` (JavaScript)。

可参阅[持久计时器文章](durable-functions-timers.md)了解有关持久计时器的更多信息和示例

### <a name="external-events"></a>外部事件

业务流程协调程序函数可以等待外部事件来更新业务流程实例。 Durable Functions 的此功能对于处理人机交互或其他外部回调通常比较有用。

可参阅[外部事件文章](durable-functions-external-events.md)了解更多信息和示例。

### <a name="error-handling"></a>错误处理。

Durable Function 业务流程采用代码实现，并可使用编程语言的错误处理功能。  这意味着可在业务流程中使用诸如“try/catch”这样的模式。  Durable Functions 还内置有一些重试策略。  操作可以在异常时自动延迟和重试活动。  重试使你能够处理暂时性异常，而无需放弃业务流程。

可参阅[错误处理文章](durable-functions-error-handling.md)了解更多信息和示例。

### <a name="cross-function-app-communication"></a>跨函数应用通信

虽然持久业务流程通常位于单个函数应用的上下文中，但可通过某些模式跨多个函数应用协调业务流程。  尽管可以通过 HTTP 进行跨应用通信，但为每个活动使用持久框架意味着还可以跨两个应用维护持久进程。

下面提供了 C# 中跨函数应用业务流程的示例。  一项活动将启动外部业务流程。 然后，另一个活动检索并返回状态。  业务流程协调程序将等待状态完成后再继续。

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [继续阅读 Durable Functions 文档](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [安装 Durable Functions 扩展和示例](durable-functions-install.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png