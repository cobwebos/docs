---
title: Durable Functions 的绑定 - Azure
description: 如何使用 Azure Functions 的 Durable Functions 扩展的触发器和绑定。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 40b5f0f17cbb6867a6ef293a485d728141a012ef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233030"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 的绑定

[Durable Functions](durable-functions-overview.md) 扩展引入了两个新的触发器绑定，用于控制业务流程协调程序和活动函数的执行。 它还引入了输出绑定，充当 Durable Functions 运行时的客户端。

## <a name="orchestration-trigger"></a>Orchestration trigger

The orchestration trigger enables you to author [durable orchestrator functions](durable-functions-types-features-overview.md#orchestrator-functions). 此触发器支持启动新的业务流程协调程序函数实例和恢复“等待”任务的现有业务流程协调程序函数实例。

使用适用于 Azure Functions 的 Visual Studio 工具时，使用 [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET 属性配置业务流程触发器。

使用脚本语言（例如 JavaScript 或 C# scripting）编写业务流程协调程序函数时，由 *function.json* 文件中 `bindings` 数组的以下 JSON 对象定义业务流程协调程序触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` is the name of the orchestration that clients must use when they want to start new instances of this orchestrator function. 此属性是可选的。 如果未指定，则使用该函数的名称。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关业务流程触发器的一些注意事项：

* 单线程 - 单个调度程序线程用于单个主机实例上的所有业务流程协调程序函数执行。 为此，必须确保业务流程协调程序函数代码有效，且不执行任何 I/O 操作。 还必须确保此线程不执行任何异步工作，等待特定于 Durable Functions 的任务类型除外。
* **有害消息处理** - 业务流程触发器中不支持有害消息。
* 消息可见性 - 业务流程触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表。 业务流程客户端绑定可以查询这些值，后文会对此进行介绍。

> [!WARNING]
> 业务流程协调程序函数不得使用业务流程触发器绑定之外的任何输入或输出绑定。 这样做有可能导致 Durable Task 扩展出现问题，因为这些绑定可能不遵从单线程处理和 I/O 规则。 If you'd like to use other bindings, add them to an Activity function called from your Orchestrator function.

> [!WARNING]
> 绝不应当将 JavaScript 业务流程协调程序函数声明为 `async`。

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

业务流程触发器绑定同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* **inputs** - .NET orchestration functions support only `DurableOrchestrationContext` as a parameter type. 不支持在函数签名中直接反序列化输入。 Code must use the `GetInput<T>` (.NET) or `getInput` (JavaScript) method to fetch orchestrator function inputs. 这些输入必须是 JSON 可序列化类型。
* 输出 - 业务流程触发器支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果 .NET 函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。

### <a name="trigger-sample"></a>触发器示例

The following example code shows what the simplest "Hello World" orchestrator function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> The `context` object in JavaScript does not represent the DurableOrchestrationContext, but the [function context as a whole](../functions-reference-node.md#context-object). 可以通过 `context` 对象的 `df` 属性访问业务流程方法。

> [!NOTE]
> JavaScript 业务流程协调程序应使用 `return`。 `durable-functions` 库将负责调用 `context.done` 方法。

大多数业务流程协调程序函数会调用活动函数，因此下面的“Hello World”示例演示了如何调用活动函数：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activity trigger

The activity trigger enables you to author functions that are called by orchestrator functions, known as [activity functions](durable-functions-types-features-overview.md#activity-functions).

If you're using Visual Studio, the activity trigger is configured using the `ActivityTriggerAttribute` .NET attribute.

如果使用 VS Code 或 Azure 门户进行开发，则由 function.json 文件中 `bindings` 数组的以下 JSON 对象定义活动触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活动的名称。 This value is the name that orchestrator functions use to invoke this activity function. 此属性是可选的。 如果未指定，则使用该函数的名称。

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

* **inputs** - .NET activity functions natively use `DurableActivityContext` as a parameter type. 或者，可以使用任何 JSON 可序列化的参数类型声明活动函数。 When you use `DurableActivityContext`, you can call `GetInput<T>` to fetch and deserialize the activity function input.
* **输出** - 活动函数支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果 .NET 函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。
* **元数据** - .NET 活动函数可以绑定到 `string instanceId` 参数，以获取父业务流程的实例 ID。

### <a name="trigger-sample"></a>触发器示例

The following example code shows what a simple "Hello World" activity function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableActivityContext` instead of `IDurableActivityContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

.NET `ActivityTriggerAttribute` 绑定的默认参数类型是 `IDurableActivityContext`。 但是，.NET 活动触发器还支持直接绑定到 JSON 可序列化类型（包括基元类型），因此相同的函数可以简化为如下所示：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

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


### <a name="using-input-and-output-bindings"></a>Using input and output bindings

You can use regular input and output bindings in addition to the activity trigger binding. For example, you can take the input to your activity binding, and send a message to an EventHub using the EventHub output binding:

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

The orchestration client binding enables you to write functions that interact with orchestrator functions. These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions). 例如，可以通过以下方式对业务流程实例进行操作：

* 启动它们。
* 查询它们的状态。
* 终止它们。
* 当它们正在运行时，向它们发送事件。
* 清除实例历史记录。

If you're using Visual Studio, you can bind to the orchestration client by using the `OrchestrationClientAttribute` .NET attribute for Durable Functions 1.0. Starting in the Durable Functions 2.0, you can bind to the orchestration client by using the `DurableClientAttribute` .NET attribute.

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the orchestration trigger is defined by the following JSON object in the `bindings` array of *function.json*:

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

In .NET functions, you typically bind to `IDurableOrchestrationClient`, which gives you full access to all orchestration client APIs supported by Durable Functions. In the older Durable Functions 2.x releases, you instead bind to the `DurableOrchestrationClient` class. In JavaScript, the same APIs are exposed by the object returned from `getClient`. 客户端对象上的 API 包括：

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatively, .NET functions can bind to `IAsyncCollector<T>` where `T` is `StartOrchestrationArgs` or `JObject`.

For more information on these operations, see the `IDurableOrchestrationClient` API documentation.

### <a name="client-sample-visual-studio-development"></a>客户端示例（Visual Studio 开发）

下面的示例是启动“HelloWorld”业务流程的队列触发型函数。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> The previous JSON is for Durable Functions 2.x. For Durable Functions 1.x, you must use `orchestrationClient` instead of the `durableClient` as the trigger type. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

下面是启动新业务流程协调程序函数实例的语言特定示例。

#### <a name="c-script-sample"></a>C# Script Sample

The following sample shows how to use the durable orchestration client binding to start a new function instance from a queue-triggered C# function:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

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

## <a name="entity-trigger"></a>Entity trigger

Entity triggers allow you to author [entity functions](durable-functions-entities.md). This trigger supports processing events for a specific entity instance.

When you use the Visual Studio tools for Azure Functions, the entity trigger is configured using the `EntityTriggerAttribute` .NET attribute.

> [!NOTE]
> Entity triggers are available starting in Durable Functions 2.x.

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

Here are some notes about the entity trigger:

* **Single-threaded**: A single dispatcher thread is used to process operations for a particular entity. If multiple messages are sent to a single entity concurrently, the operations will be processed one-at-a-time.
* **Poison-message handling** - There is no poison message support in entity triggers.
* **Message visibility** - Entity trigger messages are dequeued and kept invisible for a configurable duration. 只要函数应用正常运行，这些消息的可见性就会自动更新。
* **Return values** - Entity functions do not support return values. There are specific APIs that can be used to save state or pass values back to orchestrations.

Any state changes made to an entity during its execution will be automatically persisted after execution has completed.

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

Every entity function has a parameter type of `IDurableEntityContext`, which has the following members:

* **EntityName**: the name of the currently executing entity.
* **EntityKey**: the key of the currently executing entity.
* **EntityId**: the ID of the currently executing entity.
* **OperationName**: the name of the current operation.
* **HasState**: whether the entity exists, that is, has some state. 
* **GetState\<TState>()** : gets the current state of the entity. If it does not already exist, it is created and initialized to `default<TState>`. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **GetState\<TState>(initfunction)** : gets the current state of the entity. If it does not already exist, it is created by calling the provided `initfunction` parameter. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **SetState(arg)** : creates or updates the state of the entity. The `arg` parameter must be a JSON-serializeable object or primitive.
* **DeleteState()** : deletes the state of the entity. 
* **GetInput\<TInput>()** : gets the input for the current operation. The `TInput` type parameter must be a primitive or JSON-serializeable type.
* **Return(arg)** : returns a value to the orchestration that called the operation. The `arg` parameter must be a primitive or JSON-serializeable object.
* **SignalEntity(EntityId, operation, input)** : sends a one-way message to an entity. The `operation` parameter must be a non-null string, and the `input` parameter must be a primitive or JSON-serializeable object.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : starts a new orchestration. The `input` parameter must be a primitive or JSON-serializeable object.

The `IDurableEntityContext` object passed to the entity function can be accessed using the `Entity.Current` async-local property. This approach is convenient when using the class-based programming model.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger sample (C# function-based syntax)

以下代码是作为持久函数实现的简单 *Counter* 实体示例。 此函数定义三个操作：`add`、`reset` 和 `get`，每个操作针对整数状态运行。

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

有关基于函数的语法及其用法的详细信息，请参阅[基于函数的语法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger sample (C# class-based syntax)

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

此实体的状态是 `Counter` 类型的对象，该对象包含存储计数器当前值的字段。 为了将此对象持久保存在存储中，[Json.NET](https://www.newtonsoft.com/json) 库会将其序列化和反序列化。 

有关基于类的语法及其用法的详细信息，请参阅[定义实体类](durable-functions-dotnet-entities.md#defining-entity-classes)。

> [!NOTE]
> 使用实体类时，必须将具有 `[FunctionName]` 属性的函数入口点方法声明为 `static`。 非静态入口点方法可能会导致多次进行对象初始化，并可能导致其他不确定的行为。

Entity classes have special mechanisms for interacting with bindings and .NET dependency injection. For more information, see [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Trigger sample (JavaScript)

The following code is an example of a simple *Counter* entity implemented as a durable function written in JavaScript. 此函数定义三个操作：`add`、`reset` 和 `get`，每个操作针对整数状态运行。

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> 从 `durable-functions` npm 包版本 1.3.0 开始，JavaScript 中提供了持久实体。

## <a name="entity-client"></a>Entity client

The entity client binding enables you to asynchronously trigger [entity functions](#entity-trigger). These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions).

If you're using Visual Studio, you can bind to the entity client by using the `DurableClientAttribute` .NET attribute.

> [!NOTE]
> The `[DurableClientAttribute]` can also be used to bind to the [orchestration client](#orchestration-client).

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the entity trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 This value must match the value used by the target entity functions.
* `connectionName` - 包含存储帐户连接字符串的应用设置的名称。 The storage account represented by this connection string must be the same one used by the target entity functions. 如果未指定，则使用函数应用的默认存储帐户连接字符串。

> [!NOTE]
> In most cases, we recommend that you omit the optional properties and rely on the default behavior.

### <a name="entity-client-usage"></a>Entity client usage

In .NET functions, you typically bind to `IDurableEntityClient`, which gives you full access to all client APIs supported by Durable Entities. You can also bind to the `IDurableOrchestrationClient` interface, which provides access to client APIs for both entities and orchestrations. 客户端对象上的 API 包括：

* **ReadEntityStateAsync\<T>** : reads the state of an entity. It returns a response that indicates whether the target entity exists, and if so, what its state is.
* **SignalEntityAsync**: sends a one-way message to an entity, and waits for it to be enqueued.

There is no need to create the target entity before sending a signal - the entity state can be created from within the entity function that handles the signal.

> [!NOTE]
> It's important to understand that the "signals" sent from the client are simply enqueued, to be processed asynchronously at a later time. In particular, the `SignalEntityAsync` usually returns before the entity even starts the operation, and it is not possible to get back the return value or observe exceptions. If stronger guarantees are required (e.g. for workflows), *orchestrator functions* should be used, which can wait for entity operations to complete, and can process return values and observe exceptions.

### <a name="example-client-signals-entity-directly---c"></a>Example: client signals entity directly - C#

Here is an example queue-triggered function that invokes a "Counter" entity.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Example: client signals entity via interface - C#

Where possible, we recommend [accessing entities through interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) because it provides more type checking. For example, suppose the `Counter` entity mentioned earlier implemented an `ICounter` interface, defined as follows:

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

Client code can then use `SignalEntityAsync<ICounter>` to generate a type-safe proxy:

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

The `proxy` parameter is a dynamically generated instance of `ICounter`, which internally translates the call to `Add` into the equivalent (untyped) call to `SignalEntityAsync`.

> [!NOTE]
> The `SignalEntityAsync` APIs represent one-way operations. If an entity interfaces returns `Task<T>`, the value of the `T` parameter will always be null or `default`.

In particular, it does not make sense to signal the `Get` operation, as no value is returned. Instead, clients can use either `ReadStateAsync` to access the counter state directly, or can start an orchestrator function that calls the `Get` operation.

### <a name="example-client-signals-entity---javascript"></a>Example: client signals entity - JavaScript

Here is an example queue-triggered function that signals a "Counter" entity in JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> 从 `durable-functions` npm 包版本 1.3.0 开始，JavaScript 中提供了持久实体。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 设置

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Built-in HTTP API reference for instance management](durable-functions-http-api.md)
