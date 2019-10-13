---
title: Durable Functions 的绑定 - Azure
description: 如何使用 Azure Functions 的 Durable Functions 扩展的触发器和绑定。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 1b056ce8afe86fcd6629aff23ac95acae02ed9ba
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299875"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 的绑定

[Durable Functions](durable-functions-overview.md) 扩展引入了两个新的触发器绑定，用于控制业务流程协调程序和活动函数的执行。 它还引入了输出绑定，充当 Durable Functions 运行时的客户端。

## <a name="orchestration-trigger"></a>业务流程触发器

业务流程触发器可用于创作[持久业务流程协调程序函数](durable-functions-types-features-overview.md#orchestrator-functions)。 此触发器支持启动新的业务流程协调程序函数实例和恢复“等待”任务的现有业务流程协调程序函数实例。

使用适用于 Azure Functions 的 Visual Studio 工具时，使用 [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET 属性配置业务流程触发器。

使用脚本语言（例如 JavaScript 或 C# scripting）编写业务流程协调程序函数时，由 *function.json* 文件中 `bindings` 数组的以下 JSON 对象定义业务流程协调程序触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` 是业务流程的名称。 这是客户端想要启动此业务流程协调程序函数的新实例时必须使用的值。 此属性是可选的。 如果未指定，则使用该函数的名称。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关业务流程触发器的一些注意事项：

* 单线程 - 单个调度程序线程用于单个主机实例上的所有业务流程协调程序函数执行。 为此，必须确保业务流程协调程序函数代码有效，且不执行任何 I/O 操作。 还必须确保此线程不执行任何异步工作，等待特定于 Durable Functions 的任务类型除外。
* **有害消息处理** - 业务流程触发器中不支持有害消息。
* 消息可见性 - 业务流程触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表。 业务流程客户端绑定可以查询这些值，后文会对此进行介绍。

> [!WARNING]
> 业务流程协调程序函数不得使用业务流程触发器绑定之外的任何输入或输出绑定。 这样做有可能导致 Durable Task 扩展出现问题，因为这些绑定可能不遵从单线程处理和 I/O 规则。 若要使用其他绑定，请将它们添加到从业务流程协调程序函数调用的活动函数。

> [!WARNING]
> 绝不应当将 JavaScript 业务流程协调程序函数声明为 `async`。

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

业务流程触发器绑定同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* **输入** - .NET 业务流程函数仅支持以 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) 作为参数类型。 不支持在函数签名中直接反序列化输入。 代码必须使用 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) 或 `getInput` (JavaScript) 方法提取业务流程协调程序函数输入。 这些输入必须是 JSON 可序列化类型。
* 输出 - 业务流程触发器支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果 .NET 函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。

### <a name="trigger-sample"></a>触发器示例

以下示例代码演示了最简单的“Hello World”业务流程协调程序函数的形式：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript 中的 `context` 对象并非表示 DurableOrchestrationContext，而是表示[整个函数上下文](../functions-reference-node.md#context-object)。 可以通过 `context` 对象的 `df` 属性访问业务流程方法。

> [!NOTE]
> JavaScript 业务流程协调程序应使用 `return`。 `durable-functions` 库将负责调用 `context.done` 方法。

大多数业务流程协调程序函数会调用活动函数，因此下面的“Hello World”示例演示了如何调用活动函数：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>活动触发器

使用活动触发器可以创作业务流程协调程序函数调用的函数（称为[活动函数](durable-functions-types-features-overview.md#activity-functions)）。

如果使用的是 Visual Studio，活动触发器使用 [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET 属性进行配置。

如果使用 VS Code 或 Azure 门户进行开发，则由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义活动触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活动的名称。 此值是业务流程协调程序函数用来调用此活动函数的名称。 此属性是可选的。 如果未指定，则使用该函数的名称。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一个轮询队列。 这个队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置此队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关活动触发器的一些注意事项：

* 线程处理 - 与业务流程触发器不同，活动触发器没有关于线程处理或 I/O 的任何限制。 可以将它们视为常规功能。
* **有害消息处理** - 活动触发器不支持有害消息。
* 消息可见性 - 活动触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表。

> [!WARNING]
> 活动函数的存储后端属于实现详细信息，用户代码不得直接与这些存储实体进行交互。

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

类似于业务流程触发器，活动触发器绑定也同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* **输入** - .NET 活动函数以本机方式使用 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 作为参数类型。 或者，可以使用任何 JSON 可序列化的参数类型声明活动函数。 如果使用 `DurableActivityContext`，可以调用 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) 来提取和反序列化活动函数输入。
* **输出** - 活动函数支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果 .NET 函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。
* **元数据** - .NET 活动函数可以绑定到 `string instanceId` 参数，以获取父业务流程的实例 ID。

### <a name="trigger-sample"></a>触发器示例

以下示例代码演示了简单的“Hello World”活动函数的形式：

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

.NET `ActivityTriggerAttribute` 绑定的默认参数类型是 `DurableActivityContext`。 但是，.NET 活动触发器还支持直接绑定到 JSON 可序列化类型（包括基元类型），因此相同的函数可以简化为如下所示：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 绑定可以还作为附加参数进行传入，因此，同一函数可以简化如下：

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>使用输入和输出绑定

除了活动触发器绑定以外，还可以使用普通的输入和输出绑定。 例如，可将输入提取到活动绑定，并使用“事件中心”输出绑定向事件中心发送消息：

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>业务流程客户端

通过业务流程客户端绑定，可以编写与业务流程协调程序函数进行交互的函数。 这些函数有时称为[客户端函数](durable-functions-types-features-overview.md#client-functions)。 例如，可以通过以下方式对业务流程实例进行操作：

* 启动它们。
* 查询它们的状态。
* 终止它们。
* 当它们正在运行时，向它们发送事件。
* 清除实例历史记录。

如果使用 Visual Studio，可以使用 Durable Functions 1.0 的 [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET 属性绑定到业务流程客户端。 从 Durable Functions 2.0 预览版开始，可以使用 `DurableClientAttribute` .NET 属性绑定到业务流程客户端。

如果使用脚本语言（例如， *.csx* 或 *.js* 文件）进行开发，由 *function.json* 的 `bindings` 数组中的以下 JSON 对象定义业务流程触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 此值必须与目标业务流程协调程序函数所使用的值匹配。
* `connectionName` - 包含存储帐户连接字符串的应用设置的名称。 此连接字符串表示的存储帐户必须与目标业务流程协调程序函数所用的存储帐户相同。 如果未指定，则使用函数应用的默认存储帐户连接字符串。

> [!NOTE]
> 在大多数情况下，建议忽略这些属性，并依靠默认行为。

### <a name="client-usage"></a>客户端使用情况

在 .NET 函数中，通常会绑定到 `DurableOrchestrationClient`，后者可提供对 Durable Functions 支持的所有客户端 API 的完全访问权限。 从 Durable Functions 2.0 开始，应改为绑定到 `IDurableOrchestrationClient` 接口。 在 JavaScript 中，相同的 API 是由从 `getClient` 返回的对象公开的。 客户端对象上的 API 包括：

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

另外，还可以将 .NET 函数绑定到 `IAsyncCollector<T>`，其中 `T` 是 [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) 或 `JObject`。

有关这些操作的详细信息，请参阅 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API 文档。

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

如果未使用 Visual Studio 进行开发，则可以创建以下 *function.json* 文件。 此示例演示如何配置使用持久业务流程客户端绑定的队列触发型函数：

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
      "direction": "in"
    }
  ]
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

#### <a name="javascript-sample"></a>JavaScript 示例

下面的示例演示如何使用持久业务流程客户端绑定从 JavaScript 函数启动新函数实例：

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

有关启动实例的更多详细信息，请参阅[实例管理](durable-functions-instance-management.md)。

## <a name="entity-trigger"></a>实体触发器

使用实体触发器可以创作[实体函数](durable-functions-entities.md)。 此触发器支持处理特定实体实例的事件。

使用适用于 Azure Functions 的 Visual Studio 工具时，需使用 `EntityTriggerAttribute` .NET 属性配置实体触发器。

> [!NOTE]
> 实体触发器在 Durable Functions 2.0 及更高版本中可用。 实体触发器目前不适用于 JavaScript。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关实体触发器的一些注意事项：

* **单线程**：使用单个调度程序线程来处理特定实体的操作。 如果将多个消息同时发送到单个实体，将会逐个处理操作。
* **有害消息处理** - 实体触发器中不支持有害消息。
* **消息可见性** - 实体触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* **返回值** - 实体函数不支持返回值。 可以使用特定的 API 来保存状态，或者将值传回到业务流程。

在执行实体期间对实体所做的任何状态更改将在执行完成后自动保留。

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

每个实体函数具有参数类型 `IDurableEntityContext`，其中包含以下成员：

* **EntityName**：当前正在执行的实体的名称。
* **EntityKey**：当前正在执行的实体的键。
* **EntityId**：当前正在执行的实体的 ID。
* **OperationName**：当前操作的名称。
* **HasState**：实体是否存在，即是否存在某种状态。 
* **GetState\<TState>()** ：获取实体的当前状态。 如果它尚不存在，则将创建它并将其初始化为 `default<TState>`。 `TState` 参数必须是基元或 JSON 可序列化类型。 
* **GetState @ no__t-1TState > （initfunction）** ：获取实体的当前状态。 如果它尚不存在，则通过调用提供的 @no__t 0 参数创建它。 `TState` 参数必须是基元或 JSON 可序列化类型。 
* **SetState （arg）** ：创建或更新实体的状态。 @No__t 的参数必须是 serializeable 对象或基元。
* **DeleteState （）** ：删除实体的状态。 
* **GetInput\<TInput>()** ：获取当前操作的输入。 @No__t-0 类型参数必须是基元或 serializeable 类型。
* **Return （arg）** ：将值返回到调用该操作的业务流程。 `arg` 参数必须是基元或 JSON 可序列化对象。
* **SignalEntity （EntityId，操作，输入）** ：向实体发送单向消息。 @No__t 参数必须为非 null 字符串，`input` 参数必须是基元或 serializeable 对象。
* **CreateNewOrchestration （orchestratorFunctionName，input）** ：启动新的业务流程。 `input` 参数必须是基元或 JSON 可序列化对象。

传递给 entity 函数的 @no__t 0 对象可使用 `Entity.Current` async 本地属性进行访问。 使用基于类的编程模型时，此方法非常方便。

### <a name="trigger-sample-function-based-syntax"></a>触发器示例（基于函数的语法）

下面的代码是作为持久函数实现的简单*计数器*实体的示例。 此函数定义三个操作： `add`、`reset` 和 `get`，其中每个操作都针对一个整数状态运行。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

有关基于函数的语法以及如何使用它的详细信息，请参阅[基于函数的语法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="trigger-sample-class-based-syntax"></a>触发器示例（基于类的语法）

以下示例是使用类和方法的 `Counter` 实体的等效实现。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

此实体的状态是 `Counter` 类型的对象，该对象包含存储计数器的当前值的字段。 若要在存储中持久保存此对象， [Json.NET](https://www.newtonsoft.com/json)库将对其进行序列化和反序列化。 

有关基于类的语法以及如何使用它的详细信息，请参阅[定义实体类](durable-functions-dotnet-entities.md#defining-entity-classes)。

> [!NOTE]
> 使用实体类时，必须将具有 `[FunctionName]` 属性的函数入口点方法声明为 `static`。 非静态入口点方法可能会导致多次进行对象初始化，并可能导致其他不确定的行为。

实体类使用特殊的机制来与绑定和 .NET 依赖项注入交互。 有关详细信息，请参阅[实体构造](durable-functions-dotnet-entities.md#entity-construction)。

## <a name="entity-client"></a>实体客户端

使用实体客户端绑定可以异步触发[实体函数](#entity-trigger)。 这些函数有时称为[客户端函数](durable-functions-types-features-overview.md#client-functions)。

如果使用 Visual Studio，则可以使用 `DurableClientAttribute` .NET 属性绑定到实体客户端。

> [!NOTE]
> 还可以使用 `[DurableClientAttribute]` 绑定到[业务流程客户端](#orchestration-client)。

如果使用脚本语言（例如， *.csx* 或 *.js* 文件）进行开发，由 *function.json* 的 `bindings` 数组中的以下 JSON 对象定义实体触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 此值必须与目标实体函数所使用的值匹配。
* `connectionName` - 包含存储帐户连接字符串的应用设置的名称。 此连接字符串表示的存储帐户必须与目标实体函数所用的存储帐户相同。 如果未指定，则使用函数应用的默认存储帐户连接字符串。

> [!NOTE]
> 在大多数情况下，建议忽略可选属性，并依赖默认行为。

### <a name="entity-client-usage"></a>实体客户端的用法

在 .NET 函数中，通常会绑定到 `IDurableEntityClient`，后者可提供对持久实体支持的所有客户端 API 的完全访问权限。 也可以绑定到 `IDurableClient` 接口，该接口提供对实体和业务流程的客户端 API 的访问。 客户端对象上的 API 包括：

* **ReadEntityStateAsync\<T>** ：读取实体的状态。 它将返回一个响应，该响应指示目标实体是否存在，如果存在，则返回该实体的状态。
* **SignalEntityAsync**：将单向消息发送到实体，并等待消息排队。

在发送信号之前，无需创建目标实体-可从处理信号的实体函数内创建实体状态。

> [!NOTE]
> 必须了解的是，客户端发送的 "信号" 只是排队的，以便以后异步处理。 具体而言，`SignalEntityAsync` 通常在实体启动操作之前返回，因此不能返回返回值或观察异常。 如果需要更强的保证（例如，对于工作流），则应使用业务流程*协调程序函数*，该函数可等待实体操作完成，并可处理返回值和观察异常。

### <a name="example-client-signals-entity-directly"></a>示例：直接发送客户端信号实体

下面是一个调用“计数器”实体的示例队列触发的函数。

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface"></a>示例：通过接口的客户端信号实体

如果可能，我们建议[通过接口访问实体](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因为它提供更多的类型检查。 例如，假设前面所述的 `Counter` 实体实现 `ICounter` 接口，定义如下：

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

然后，客户端代码可以使用 `SignalEntityAsync<ICounter>` 生成类型安全的代理：

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

@No__t 的参数是动态生成的 `ICounter` 的实例，它在内部将对 `Add` 的调用转换为等效的（非类型化的）调用 `SignalEntityAsync`。

> [!NOTE]
> `SignalEntityAsync` API 表示单向操作。 如果实体接口返回 `Task<T>`，则 `T` 参数的值始终为 null 或 `default`。

特别是，无需向 `Get` 操作发出信号，因为不返回值。 相反，客户端可以使用 `ReadStateAsync` 直接访问计数器状态，也可以启动调用 @no__t 1 操作的业务流程协调程序函数。 

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 设置

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于实例管理的内置 HTTP API 参考](durable-functions-http-api.md)
