---
title: Durable Functions 中的任务中心 - Azure
description: 了解在 Azure Functions 的 Durable Functions 扩展中什么是任务中心。 Learn how to configure task hubs.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 38c7da8a1de57ed5acf3248fc6a71431de0bd1e2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232790"
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
* A storage container containing large message payloads, if applicable.

All of these resources are created automatically in the default Azure Storage account when orchestrator, entity, or activity functions run or are scheduled to run. [性能和缩放](durable-functions-perf-and-scale.md)一文介绍了如何使用这些资源。

## <a name="task-hub-names"></a>任务中心名称

任务中心由 *host.json* 文件中声明的名称标识，如以下示例所示：

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Task hubs can also be configured using app settings, as shown in the following `host.json` example file:

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

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

The following code is a precompiled C# example of how to write a function that uses the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to work with a task hub that is configured as an App Setting:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
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

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript"></a>JavaScript

`function.json` 文件中的任务中心属性通过应用设置进行设置：

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

任务中心名称必须以字母开头且只能包含字母和数字。 If not specified, a default task hub name will be used as shown in the following table:

| Durable extension version | Default task hub name |
| - | - |
| 2.x | When deployed in Azure, the task hub name is derived from the name of the _function app_. When running outside of Azure, the default task hub name is `TestHubName`. |
| 1.x | The default task hub name for all environments is `DurableFunctionsHub`. |

For more information about the differences between extension versions, see the [Durable Functions versions](durable-functions-versions.md) article.

> [!NOTE]
> 当共享存储帐户中有多个任务中心时，名称用于将一个任务中心与其他任务中心区分开来。 如果有多个函数应用共享一个共享存储帐户，则必须在 host.json 文件中为每个任务中心显式配置不同的名称。 Otherwise the multiple function apps will compete with each other for messages, which could result in undefined behavior, including orchestrations getting unexpectedly "stuck" in the `Pending` or `Running` state.

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Learn how to handle orchestration versioning](durable-functions-versioning.md)
