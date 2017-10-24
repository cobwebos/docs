---
title: "Durable Functions 的绑定 - Azure"
description: "如何使用 Azure Functions 的 Durable Functions 扩展的触发器和绑定。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 67fbc1f18dd168e2fad8011bc30b895894d98942
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 的绑定

[Durable Functions](durable-functions-overview.md) 扩展引入了两个新的触发器绑定，用于控制业务流程协调程序和活动函数的执行。 它还引入了输出绑定，充当 Durable Functions 运行时的客户端。

## <a name="orchestration-triggers"></a>业务流程触发器

业务流程触发器可用于创作持久业务流程协调程序函数。 此触发器支持启动新的业务流程协调程序函数实例和恢复“等待”任务的现有业务流程协调程序函数实例。

使用适用于 Azure Functions 的 Visual Studio 工具时，使用 [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET 属性配置业务流程触发器。

使用脚本语言编写业务流程协调程序函数时，由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义业务流程协调程序触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "version": "<Optional - version label of this orchestrator function>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` 是业务流程的名称。 这是客户端想要启动此业务流程协调程序函数的新实例时必须使用的值。 此属性是可选的。 如果未指定，则使用该函数的名称。
* `version` 是业务流程的版本标签。 启动业务流程的新实例的客户端必须包含匹配的版本标签。 此属性是可选的。 如果未指定，则使用空字符串。 有关版本控制的详细信息，请参阅[版本控制](durable-functions-versioning.md)。

> [!NOTE]
> 此时不建议设置 `orchestration` 或 `version` 属性的值。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关业务流程触发器的一些注意事项：

* 单线程 - 单个调度程序线程用于单个主机实例上的所有业务流程协调程序函数执行。 为此，必须确保业务流程协调程序函数代码有效，且不执行任何 I/O 操作。 还必须确保此线程不执行任何异步工作，等待特定于 Durable Functions 的任务类型除外。
* 有害消息处理 - 业务流程触发器中不支持有害消息。
* 消息可见性 - 业务流程触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表。 业务流程客户端绑定可以查询这些值，后文会对此进行介绍。

> [!WARNING]
> 业务流程协调程序函数不得使用业务流程触发器绑定之外的任何输入或输出绑定。 这样做有可能导致 Durable Task 扩展出现问题，因为这些绑定可能不遵从单线程处理和 I/O 规则。

### <a name="trigger-usage"></a>触发器使用情况

业务流程触发器绑定同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* 输入 - 业务流程函数仅支持 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) 作为参数类型。 不支持函数签名中直接包含的反序列化输入。 代码必须使用 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) 方法提取业务流程协调程序函数输入。 这些输入必须是 JSON 可序列化类型。
* 输出 - 业务流程触发器支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。

> [!NOTE]
> 目前仅支持使用 C# 的业务流程触发器。

### <a name="trigger-sample"></a>触发器示例

下面的示例显示了最简单的“Hello World”C# 业务流程协调程序函数的形式：

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

大多数业务流程协调程序函数会调用其他函数，因此下面的“Hello World”示例演示了如何调用函数：

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = await context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

## <a name="activity-triggers"></a>活动触发器

通过活动触发器，可以创作业务流程协调程序函数调用的函数。

如果使用 Visual Studio，则使用 [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET 属性配置活动触发器。 

如果使用 Azure 门户进行开发，则由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义活动触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "version": "<Optional - version label of this activity function>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活动的名称。 这是业务流程协调程序函数用来调用此活动函数的值。 此属性是可选的。 如果未指定，则使用该函数的名称。
* `version` 是活动的版本标签。 调用活动的业务流程协调程序函数必须包含匹配的版本标签。 此属性是可选的。 如果未指定，则使用空字符串。 有关详细信息，请参阅[版本控制](durable-functions-versioning.md)。

> [!NOTE]
> 此时不建议设置 `activity` 或 `version` 属性的值。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一个轮询队列。 这个队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置此队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关活动触发器的一些注意事项：

* 线程处理 - 与业务流程触发器不同，活动触发器没有关于线程处理或 I/O 的任何限制。 可以将它们视为常规功能。
* 有害消息处理 - 活动触发器中不支持有害消息。
* 消息可见性 - 活动触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表。

> [!WARNING]
> 活动函数的存储后端属于实现详细信息，用户代码不得直接与这些存储实体进行交互。

### <a name="trigger-usage"></a>触发器使用情况

类似于业务流程触发器，活动触发器绑定也同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* 输入 - 活动函数以本机方式使用 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 作为参数类型。 或者，可以使用任何 JSON 可序列化的参数类型声明活动函数。 如果使用 `DurableActivityContext`，可以调用 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) 来提取和反序列化活动函数输入。
* 输出 - 活动触发器支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。
* 元数据 - 活动函数可以绑定到 `string instanceId` 参数，以获取父业务流程的实例 ID。

> [!NOTE]
> 目前，Node.js 函数中不支持活动触发器。

### <a name="trigger-sample"></a>触发器示例

下面的示例显示了简单的“Hello World”C# 活动函数的形式：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

`ActivityTriggerAttribute` 绑定的默认参数类型是 `DurableActivityContext`。 但是，活动触发器还支持直接绑定到 JSON 可序列化类型（包括基元类型），因此相同的函数可以简化为如下所示：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

## <a name="orchestration-client"></a>业务流程客户端

通过业务流程客户端绑定，可以编写与业务流程协调程序函数进行交互的函数。 例如，可以通过以下方式对业务流程实例进行操作：
* 启动它们。
* 查询它们的状态。
* 终止它们。
* 当它们正在运行时，向它们发送事件。

如果使用 Visual Studio，可以使用 [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET 属性绑定到业务流程客户端。

如果使用脚本语言（例如，.csx 文件）进行开发，由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义业务流程触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 此值必须与目标业务流程协调程序函数所使用的值匹配。
* `connectionName` - 包含存储连接字符串的应用设置的名称。 此连接字符串表示的存储帐户必须与目标业务流程协调程序函数所用的存储帐户相同。 如果未指定，则使用函数应用的默认连接字符串。

> [!NOTE]
> 在大多数情况下，建议忽略这些属性，并依靠默认行为。

### <a name="client-usage"></a>客户端使用情况

在 C# 函数中，通常会绑定到 `DurableOrchestrationClient`，后者可提供对 Durable Functions 支持的所有客户端 API 的完全访问权限。 客户端对象上的 API 包括：

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

或者，可以绑定到 `IAsyncCollector<T>`，其中 `T` 是 [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) 或 `JObject`。

有关这些操作的其他详细信息，请参阅 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API 文档。

### <a name="client-sample-visual-studio-development"></a>客户端示例（Visual Studio 开发）

下面的示例是启动“HelloWorld”业务流程的队列触发型函数。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>客户端示例（非 Visual Studio）

如果未使用 Visual Studio 进行开发，则可以创建以下 function.json 文件。 此示例演示如何配置使用持久业务流程客户端绑定的队列触发型函数：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

下面是启动新业务流程协调程序函数实例的语言特定示例。

#### <a name="c-sample"></a>C# 示例

下面的示例演示如何使用持久业务流程客户端绑定从 C# 脚本函数启动新函数实例：

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="nodejs-sample"></a>Node.js 示例

下面的示例演示如何使用持久业务流程客户端绑定从 Node.js 函数启动新函数实例：

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

有关启动实例的更多详细信息，请参阅[实例管理](durable-functions-instance-management.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解检查点行为和重播行为](durable-functions-checkpointing-and-replay.md)

