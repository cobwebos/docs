---
title: Durable Functions 的绑定 - Azure
description: 如何使用 Azure Functions 的 Durable Functions 扩展的触发器和绑定。
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278215"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 的绑定

[Durable Functions](durable-functions-overview.md) 扩展引入了两个新的触发器绑定，用于控制业务流程协调程序和活动函数的执行。 它还引入了输出绑定，充当 Durable Functions 运行时的客户端。

## <a name="orchestration-trigger"></a>业务流程触发器

业务流程触发器允许您创作持久的业务流程[协调程序函数](durable-functions-types-features-overview.md#orchestrator-functions)。 此触发器支持启动新的业务流程协调程序函数实例和恢复“等待”任务的现有业务流程协调程序函数实例。

使用适用于 Azure Functions 的 Visual Studio 工具时，使用 [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET 属性配置业务流程触发器。

使用脚本语言（例如 JavaScript 或 C# scripting）编写业务流程协调程序函数时，由 `bindings`function.json*文件中* 数组的以下 JSON 对象定义业务流程协调程序触发器：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` 是客户端想要启动此业务流程协调程序函数的新实例时必须使用的业务流程的名称。 此属性是可选的。 如果未指定，则使用该函数的名称。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

以下是有关业务流程触发器的一些注意事项：

* 单线程 - 单个调度程序线程用于单个主机实例上的所有业务流程协调程序函数执行。 为此，必须确保业务流程协调程序函数代码有效，且不执行任何 I/O 操作。 还必须确保此线程不执行任何异步工作，等待特定于 Durable Functions 的任务类型除外。
* **有害消息处理** - 业务流程触发器中不支持有害消息。
* 消息可见性 - 业务流程触发器消息会取消排队并在可配置的持续时间内保持可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* 返回值 - 返回值序列化为 JSON，并持久保存到 Azure 表存储中的业务流程历史记录表。 业务流程客户端绑定可以查询这些值，后文会对此进行介绍。

> [!WARNING]
> 业务流程协调程序函数不得使用业务流程触发器绑定之外的任何输入或输出绑定。 这样做有可能导致 Durable Task 扩展出现问题，因为这些绑定可能不遵从单线程处理和 I/O 规则。 如果你想要使用其他绑定，请将它们添加到从你的业务流程协调程序函数调用的活动函数。

> [!WARNING]
> 绝不应当将 JavaScript 业务流程协调程序函数声明为 `async`。

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

业务流程触发器绑定同时支持输入和输出。 下面是有关输入和输出处理的一些须知事项：

* **输入**-.net 业务流程函数仅支持作为参数类型 `DurableOrchestrationContext`。 不支持在函数签名中直接反序列化输入。 代码必须使用 `GetInput<T>` （.NET）或 `getInput` （JavaScript）方法才能获取 orchestrator 函数输入。 这些输入必须是 JSON 可序列化类型。
* 输出 - 业务流程触发器支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果 .NET 函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。

### <a name="trigger-sample"></a>触发器示例

下面的示例代码显示最简单的 "Hello World" orchestrator 函数如下所示：

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
> 前面的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript 中的 `context` 对象并不表示 DurableOrchestrationContext，而是[整个函数上下文](../functions-reference-node.md#context-object)。 可以通过 `context` 对象的 `df` 属性访问业务流程方法。

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
> 前面的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

#### <a name="javascript-functions-20-only"></a>JavaScript（仅限 Functions 2.0）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>活动触发器

活动触发器使你能够创作由 orchestrator 函数（称为[活动函数](durable-functions-types-features-overview.md#activity-functions)）调用的函数。

如果使用的是 Visual Studio，则使用 `ActivityTriggerAttribute` .NET 属性配置活动触发器。

如果使用 VS Code 或 Azure 门户进行开发，则由 function.json`bindings`*文件中* 数组的以下 JSON 对象定义活动触发器：

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

* **输入**-.net 活动函数以本机方式使用 `DurableActivityContext` 作为参数类型。 或者，可以使用任何 JSON 可序列化的参数类型声明活动函数。 使用 `DurableActivityContext`时，可以调用 `GetInput<T>` 来提取和反序列化活动函数输入。
* **输出** - 活动函数支持输出值以及输入。 函数的返回值用于分配输出值，且必须是 JSON 可序列化的。 如果 .NET 函数返回 `Task` 或 `void`，则会将 `null` 值保存为输出。
* **元数据** - .NET 活动函数可以绑定到 `string instanceId` 参数，以获取父业务流程的实例 ID。

### <a name="trigger-sample"></a>触发器示例

下面的示例代码演示简单的 "Hello World" 活动函数可能如下所示：

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
> 前面的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableActivityContext` 而不是 `IDurableActivityContext`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

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


### <a name="using-input-and-output-bindings"></a>使用输入和输出绑定

除了活动触发器绑定外，还可以使用常规输入和输出绑定。 例如，你可以将输入用于活动绑定，并使用 EventHub 输出绑定将消息发送到 EventHub：

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

业务流程客户端绑定使您能够编写与 orchestrator 函数交互的函数。 这些函数有时称为[客户端函数](durable-functions-types-features-overview.md#client-functions)。 例如，可以通过以下方式对业务流程实例进行操作：

* 启动它们。
* 查询它们的状态。
* 终止它们。
* 当它们正在运行时，向它们发送事件。
* 清除实例历史记录。

如果使用的是 Visual Studio，则可以使用 Durable Functions 1.0 的 `OrchestrationClientAttribute` .NET 特性绑定到业务流程客户端。 从 Durable Functions 2.0 开始，可以通过使用 `DurableClientAttribute` .NET 特性绑定到业务流程客户端。

如果要使用脚本语言（例如， *run.csx*或 *.js*文件）进行开发，则该业务流程触发器由*函数*`bindings` 数组中的以下 json 对象定义：

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

在 .NET 函数中，通常绑定到 `IDurableOrchestrationClient`，这使你可以完全访问 Durable Functions 支持的所有业务流程客户端 Api。 在较旧的 Durable Functions 2.x 版本中，你可以绑定到 `DurableOrchestrationClient` 类。 在 JavaScript 中，同一 Api 由 `getClient`中返回的对象公开。 客户端对象上的 API 包括：

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

或者，.NET 函数可以绑定到 `T` `StartOrchestrationArgs` 或 `JObject``IAsyncCollector<T>`。

有关这些操作的详细信息，请参阅 `IDurableOrchestrationClient` API 文档。

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
> 前面C#的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `OrchestrationClient` 属性而不是 `DurableClient` 属性，并且必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

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
> 以前的 JSON 适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `orchestrationClient` 而不是 `durableClient` 作为触发器类型。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

下面是启动新业务流程协调程序函数实例的语言特定示例。

#### <a name="c-script-sample"></a>C#脚本示例

下面的示例演示如何使用持久业务流程客户端绑定从队列触发C#的函数启动新函数实例：

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> 前面的代码适用于 Durable Functions 1.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationClient` 参数类型，而不是 `IDurableOrchestrationClient`。 有关各版本之间的差异的详细信息，请参阅[Durable Functions 版本](durable-functions-versions.md)一文。

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

实体触发器允许您创作[实体函数](durable-functions-entities.md)。 此触发器支持处理特定实体实例的事件。

当你使用 Visual Studio tools for Azure Functions 时，实体触发器使用 `EntityTriggerAttribute` .NET 属性进行配置。

> [!NOTE]
> 实体触发器从 Durable Functions 1.x 开始可用。

本质上，此触发器绑定轮询函数应用的默认存储帐户中的一系列队列。 这些队列是扩展的内部实现详细信息，因此未在绑定属性中显式配置这些队列。

### <a name="trigger-behavior"></a>触发器行为

下面是有关实体触发器的一些注意事项：

* **单线程**：单个调度程序线程用于处理特定实体的操作。 如果将多个消息同时发送到一个实体，则每次处理一次操作。
* **病毒消息处理**-实体触发器中不支持有害消息。
* **消息可见性**-实体触发器消息会取消排队并在可配置的持续时间内保持不可见。 只要函数应用正常运行，这些消息的可见性就会自动更新。
* **返回值**-实体函数不支持返回值。 有一些特定的 Api 可用于保存状态或将值传递回业务流程。

执行完成后，将自动保留在执行过程中对实体所做的任何状态更改。

### <a name="trigger-usage-net"></a>触发器用法 (.NET)

每个实体函数都具有 `IDurableEntityContext`的参数类型，其中包含以下成员：

* **EntityName**：当前正在执行的实体的名称。
* **EntityKey**：当前正在执行的实体的键。
* **EntityId**：当前正在执行的实体的 ID。
* **OperationName**：当前操作的名称。
* **HasState**：实体是否存在，即是否存在某种状态。 
* **GetState\<TState > （）** ：获取实体的当前状态。 如果它尚不存在，则将创建它并将其初始化为 `default<TState>`。 `TState` 参数必须是基元类型或 serializeable 类型。 
* **GetState\<TState > （initfunction）** ：获取实体的当前状态。 如果它尚不存在，则通过调用提供的 `initfunction` 参数来创建它。 `TState` 参数必须是基元类型或 serializeable 类型。 
* **SetState （arg）** ：创建或更新实体的状态。 `arg` 参数必须是 serializeable 对象或基元。
* **DeleteState （）** ：删除实体的状态。 
* **Getinput t>\<TInput > （）** ：获取当前操作的输入。 `TInput` 类型参数必须是基元类型或 serializeable 类型。
* **Return （arg）** ：将值返回到调用该操作的业务流程。 `arg` 参数必须是基元或 serializeable 对象。
* **SignalEntity （EntityId、scheduledTimeUtc、operation、input）** ：向实体发送单向消息。 `operation` 参数必须是一个非 null 字符串，可选 `scheduledTimeUtc` 必须是要在其上调用操作的 UTC 日期时间，`input` 参数必须是基元或 serializeable 对象。
* **CreateNewOrchestration （orchestratorFunctionName，input）** ：启动新的业务流程。 `input` 参数必须是基元或 serializeable 对象。

传递给 entity 函数的 `IDurableEntityContext` 对象可以使用 `Entity.Current` 的 async 本地属性来访问。 使用基于类的编程模型时，此方法非常方便。

### <a name="trigger-sample-c-function-based-syntax"></a>触发器示例（C#基于函数的语法）

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

### <a name="trigger-sample-c-class-based-syntax"></a>触发器示例（C#基于类的语法）

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
> 使用实体类时，必须将具有 `[FunctionName]` 属性的函数入口点方法声明为 *。* `static` 非静态入口点方法可能会导致多次进行对象初始化，并可能导致其他不确定的行为。

实体类具有用于与绑定和 .NET 依赖项注入交互的特殊机制。 有关详细信息，请参阅[实体构造](durable-functions-dotnet-entities.md#entity-construction)。

### <a name="trigger-sample-javascript"></a>触发器示例（JavaScript）

下面的代码是作为使用 JavaScript 编写的持久函数实现的简单*计数器*实体的示例。 此函数定义三个操作：`add`、`reset` 和 `get`，每个操作针对整数状态运行。

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
> 从  **npm 包版本 1.3.0 开始，JavaScript 中提供了持久实体**`durable-functions`。

## <a name="entity-client"></a>实体客户端

实体客户端绑定使你能够以异步方式触发[实体函数](#entity-trigger)。 这些函数有时称为[客户端函数](durable-functions-types-features-overview.md#client-functions)。

如果使用的是 Visual Studio，则可以使用 `DurableClientAttribute` .NET 特性绑定到实体客户端。

> [!NOTE]
> `[DurableClientAttribute]` 还可以用于绑定到[业务流程客户端](#orchestration-client)。

如果要使用脚本语言（例如， *run.csx*或 *.js*文件）进行开发，则实体触发器由*函数*`bindings` 数组中的以下 JSON 对象定义：

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` - 用于多个函数应用共享同一存储帐户但需要彼此独立的方案。 如果未指定，则使用 `host.json` 中的默认值。 此值必须与目标实体函数使用的值匹配。
* `connectionName` - 包含存储帐户连接字符串的应用设置的名称。 此连接字符串所表示的存储帐户必须与目标实体函数使用的存储帐户相同。 如果未指定，则使用函数应用的默认存储帐户连接字符串。

> [!NOTE]
> 在大多数情况下，我们建议省略可选属性，并依赖于默认行为。

### <a name="entity-client-usage"></a>实体客户端使用情况

在 .NET 函数中，通常绑定到 `IDurableEntityClient`，这使您能够完全访问持久性实体支持的所有客户端 Api。 你还可以绑定到 `IDurableOrchestrationClient` 接口，该接口提供对实体和业务流程的客户端 Api 的访问。 客户端对象上的 API 包括：

* **ReadEntityStateAsync\<t >** ：读取实体的状态。 它将返回一个响应，该响应指示目标实体是否存在，如果存在，则返回该实体的状态。
* **SignalEntityAsync**：向实体发送单向消息，并等待该消息排入队列。
* **ListEntitiesAsync**：查询多个实体的状态。 可以按*名称*和*上次操作时间*查询实体。

在发送信号之前，无需创建目标实体-可从处理信号的实体函数内创建实体状态。

> [!NOTE]
> 必须了解的是，客户端发送的 "信号" 只是排队的，以便以后异步处理。 具体而言，`SignalEntityAsync` 通常会在实体启动操作之前返回，因此不能返回返回值或观察异常。 如果需要更强的保证（例如，对于工作流），则应使用业务流程*协调程序函数*，该函数可等待实体操作完成，并可处理返回值和观察异常。

### <a name="example-client-signals-entity-directly---c"></a>示例：直接向客户端发送信号实体-C#

下面是一个示例队列触发的函数，它调用 "计数器" 实体。

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

### <a name="example-client-signals-entity-via-interface---c"></a>示例：通过接口的客户端信号实体-C#

如果可能，我们建议[通过接口访问实体](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因为它提供更多的类型检查。 例如，假设前面提到的 `Counter` 实体实现了 `ICounter` 接口，定义如下：

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

`proxy` 参数是动态生成的 `ICounter`实例，它在内部将 `Add` 对的调用转换为对 `SignalEntityAsync`的等效（非类型化）调用。

> [!NOTE]
> `SignalEntityAsync` Api 表示单向操作。 如果实体接口返回 `Task<T>`，则 `T` 参数的值将始终为 null 或 `default`。

特别是，无需对 `Get` 操作发出信号，因为不返回值。 相反，客户端可以使用 `ReadStateAsync` 直接访问计数器状态，也可以启动调用 `Get` 操作的业务流程协调程序函数。

### <a name="example-client-signals-entity---javascript"></a>示例：客户端信号实体-JavaScript

下面是一个示例队列触发的函数，用于指示 JavaScript 中的 "计数器" 实体。

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
> 从  **npm 包版本 1.3.0 开始，JavaScript 中提供了持久实体**`durable-functions`。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 设置

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于实例管理的内置 HTTP API 参考](durable-functions-http-api.md)
