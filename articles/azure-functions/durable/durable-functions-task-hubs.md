---
title: Durable Functions 中的任务中心 - Azure
description: 了解在 Azure Functions 的 Durable Functions 扩展中什么是任务中心。 了解如何配置任务中心。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 40144fb50a01a64bbd67d541562b4fe0842fbf10
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097797"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions 中的任务中心 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 中的*任务中心*是用于业务流程的 Azure 存储资源的逻辑容器。 只有当业务流程协调程序函数与活动函数属于同一任务中心时，它们才能彼此进行交互。

如果多个函数应用共享存储帐户，则必须使用单独的任务中心名称配置每个函数应用。 一个存储帐户可以包含多个任务中心。 下图说明了在共享和专用存储帐户中每个函数应用有一个任务中心。

![说明共享和专用存储帐户的关系图。](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure 存储资源

任务中心包含以下存储资源：

* 一个或多个控制队列。
* 一个工作项队列。
* 一个历史记录表。
* 一个实例表。
* 一个包含一个或多个租用 blob 的存储容器。

当业务流程协调程序或活动函数运行时或调度它们运行时，将自动在默认 Azure 存储帐户中创建所有这些资源。 [性能和缩放](durable-functions-perf-and-scale.md)一文介绍了如何使用这些资源。

## <a name="task-hub-names"></a>任务中心名称

任务中心由 *host.json* 文件中声明的名称标识，如以下示例所示：

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

还可以使用应用设置配置任务中心，如以下 *host.json* 示例文件所示：

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (Functions 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

任务中心名称将设置为 `MyTaskHub` 应用设置的值。 以下 `local.settings.json` 演示了如何将 `MyTaskHub` 设置定义为 `samplehubname`：

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

这是一个预编译的 C# 示例，说明如何编写这样一个函数，该函数使用 [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) 来处理配置为应用设置的任务中心：

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

下面是 JavaScript 所需的配置。 `function.json` 文件中的任务中心属性通过应用设置进行设置：

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，默认名称是 **DurableFunctionsHub**。

> [!NOTE]
> 当共享存储帐户中有多个任务中心时，名称用于将一个任务中心与其他任务中心区分开来。 如果有多个函数应用共享一个共享存储帐户，则必须在 host.json 文件中为每个任务中心显式配置不同的名称。 否则多个函数应用会相互竞争消息，这可能会导致未定义的行为。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理版本控制](durable-functions-versioning.md)
