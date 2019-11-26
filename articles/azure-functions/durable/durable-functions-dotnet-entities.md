---
title: 有关 .NET 中的持久实体的开发人员指南 - Azure Functions
description: 如何通过 Azure Functions 的 Durable Functions 扩展使用 .NET 中的持久实体。
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 74b013c9953974371957cc4d88439d20770d78a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231432"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET 中持久实体的开发人员指南

本文详细介绍可用于在 .NET 中开发持久实体的接口，并提供示例和一般建议。 

实体函数为无服务器应用程序开发人员提供一种便捷的方式用于将应用程序状态组织为细化实体的集合。 有关基础概念的更多详细信息，请参阅 "[持久实体：概念](durable-functions-entities.md)" 一文。

我们目前提供两个 API 用于定义实体：

- **基于类的语法**将实体和操作表示为类和方法。 此语法生成易读的代码，并允许通过接口以类型检查的方式调用操作。 

- **基于函数的语法**是将实体表示为函数的较低级别的接口。 使用该语法可以精确控制实体操作的调度方式以及实体状态的管理方式。  

本文侧重于基于类的语法，因为我们预期它更适用于大多数应用程序。 但是，对于想要定义或管理自身的实体状态和操作抽象的应用程序，可能适合使用[基于函数的语法](#function-based-syntax)。 此外，该语法可能适合用于实现需要基于类的语法所不支持的泛型的库。 

> [!NOTE]
> 基于类的语法只是建立在基于函数的语法基础之上的一个层，因此，在同一应用程序中，这两种变体可以换用。 
 
## <a name="defining-entity-classes"></a>定义实体类

以下示例是 `Counter` 实体的一个实现，该实体存储整数类型的单个值，并提供 `Add`、`Reset`、`Get` 和 `Delete` 这四个操作。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

`Run` 函数包含使用基于类的语法所需的样板。 它必须是静态的 Azure 函数。 它对实体处理的每个操作消息执行一次。 调用 `DispatchAsync<T>` 时，如果该实体尚未进入内存，该函数将构造 `T` 类型的对象，并基于存储中最后保存的 JSON（如果有）填充该对象的字段。 然后，它结合匹配的名称调用方法。

> [!NOTE]
> 基于类的实体的状态是在实体处理操作之前**隐式创建**的，可以通过调用  **在操作中**显式删除`Entity.Current.DeleteState()`。

### <a name="class-requirements"></a>类要求
 
实体类是不需要特殊超类、接口或特性的 POCO（普通旧 CLR 对象）。 但是：

- 类必须是可构造的（请参阅[实体构造](#entity-construction)）。
- 类必须是 JSON 可序列化的（请参阅[实体序列化](#entity-serialization)）。

此外，旨在作为操作调用的任何方法必须满足附加的要求：

- 一个操作最多只能有一个参数，并且不能有任何重载或泛型类型参数。
- 要使用接口从业务流程调用的操作必须返回 `Task` 或 `Task<T>`。
- 参数和返回值必须是可序列化的值或对象。

### <a name="what-can-operations-do"></a>操作的作用是什么？

所有实体操作可以读取和更新实体状态，对状态的更改将自动保存到存储中。 此外，操作可以在所有 Azure Functions 通用的限制范围内执行外部 I/O 或其他计算。

操作还可以访问 `Entity.Current` 上下文提供的功能：

* `EntityName`：当前正在执行的实体的名称。
* `EntityKey`：当前正在执行的实体的键。
* `EntityId`：当前正在执行的实体的 ID（包括名称和键）。
* `SignalEntity`：将单向消息发送到实体。
* `CreateNewOrchestration`：启动新的业务流程。
* `DeleteState`：删除此实体的状态。

例如，我们可以将计数器实体修改为在计数器达到 100 时启动某个业务流程，并将实体 ID 作为输入参数传递：

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>直接访问实体

可以使用实体及其操作的显式字符串名称来直接访问基于类的实体。 我们提供以下示例：有关基础概念的更深入说明（如信号与调用），请参阅[访问实体](durable-functions-entities.md#access-entities)中的讨论。 

> [!NOTE]
> 我们建议尽量[通过接口访问实体](#accessing-entities-through-interfaces)，因为这种方法提供更多的类型检查。

### <a name="example-client-signals-entity"></a>示例：客户端向实体发出信号

以下 Azure Http 函数使用 REST 约定实现 DELETE 操作。 它将一个删除信号发送到计数器实体，该实体的键已在 URL 路径中传递。

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>示例：客户端读取实体状态

以下 Azure Http 函数使用 REST 约定实现 GET 操作。 该函数读取计数器实体的当前状态，该实体的键已在 URL 路径中传递。

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> `ReadEntityStateAsync` 返回的对象只是一个本地副本，即，某个较早时间点的实体状态的快照。 具体而言，此状态可能已过时，修改此对象不会影响实际的实体。 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>示例：业务流程先发出信号，然后调用实体

以下业务流程向计数器实体发出信号来递增计数器，然后调用同一实体来读取其最新值。

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>通过接口访问实体

使用接口可以通过生成的代理对象访问实体。 此方法确保操作的名称和参数类型与实现的操作相匹配。 我们建议尽量使用接口来访问实体。

例如，可按如下所示修改计数器示例：

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

实体类和实体接口类似于 [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/) 普及化的粒度和粒度接口。 有关持久实体与 Orleans 之间的相似性和差异的详细信息，请参阅[与虚拟执行组件的比较](durable-functions-entities.md#comparison-with-virtual-actors)。

除了提供类型检查以外，接口可以更好地在应用程序内部实现关注点分离。 例如，由于一个实体可以实现多个接口，因此单个实体可为多个角色提供服务。 此外，由于一个接口可由多个实体实现，因此可将常规通信模式实现为可重用的库。

### <a name="example-client-signals-entity-through-interface"></a>示例：客户端通过接口向实体发出信号

客户端代码可以使用 `SignalEntityAsync<TEntityInterface>` 向实现 `TEntityInterface` 的实体发送信号。 例如：

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

在此示例中，`proxy` 参数是动态生成的 `ICounter` 实例，该实体在内部将 `Delete` 调用转换为信号。

> [!NOTE]
> `SignalEntityAsync` API 只可用于单向操作。 即使操作返回 `Task<T>`，`T` 参数的值也始终是 null 或 `default`，而不是实际结果。
例如，向 `Get` 操作发出信号没有意义，因为这不会返回任何值。 客户端可以改用 `ReadStateAsync` 来直接访问计数器状态，或者可以启动一个调用 `Get` 操作的业务流程协调程序函数。 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>示例：业务流程先发出信号，然后通过代理调用实体

若要从业务流程内部调用实体或向实体发出信号，可以结合接口类型使用 `CreateEntityProxy`，以便为实体生成代理。 然后，可以使用此代理来调用操作或向操作发出信号：

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

将隐式向返回 `void` 的操作发出信号并调用返回 `Task` 或 `Task<T>` 的任何操作。 可以更改此默认行为，并使用 `SignalEntity<IInterfaceType>` 方法显式向操作发出信号，即使操作返回 Task。

### <a name="shorter-option-for-specifying-the-target"></a>用于指定目标的简短选项

使用接口调用实体或向实体发出信号时，第一个参数必须指定目标实体。 可以通过指定实体 ID 来指定目标；如果只有一个类实现实体，则只需指定实体键：

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

如果仅指定实体键，而在运行时找不到唯一的实现，则会引发 `InvalidOperationException`。 

### <a name="restrictions-on-entity-interfaces"></a>实体接口的限制

与往常一样，所有参数和返回类型必须是 JSON 可序列化的。 否则，在运行时将引发序列化异常。

我们还强制实施其他一些规则：
* 实体接口只能定义方法。
* 实体接口不得包含泛型参数。
* 实体接口方法不能有多个参数。
* 实体接口方法必须返回 `void`、`Task` 或 `Task<T>` 

如果违反上述任意规则，在将接口用作 `InvalidOperationException` 或 `SignalEntity` 的类型参数时，在运行时会引发 `CreateProxy`。 异常消息会解释违反了哪个规则。

> [!NOTE]
> 对于返回 `void` 的接口方法，只能向其发出信号（单向），而不能调用（双向）它们。 对于返回 `Task` 或 `Task<T>` 的接口方法，可以调用它们，也可以向其发出信号。 如果调用，它们将返回操作的结果，或重新引发操作所引发的异常。 但是，在向其发出信号时，它们不会返回实际结果或操作引发的异常，而只返回默认值。

## <a name="entity-serialization"></a>实体序列化

由于实体的状态会持久保存，因此实体类必须可序列化。 Durable Functions 运行时使用 [Json.NET](https://www.newtonsoft.com/json) 库来实现此目的，该库支持使用多个策略和特性来控制序列化和反序列化过程。 最常用的 C# 数据类型（包括数组和集合类型）已经可序列化，并可轻松用于定义持久实体的状态。

例如，Json.NET 可以轻松序列化和反序列化以下类：

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>序列化特性

在以上示例中，我们已选择包含多个特性来提高基础序列化的可见性：
- 我们已使用 `[JsonObject(MemberSerialization.OptIn)]` 为类做了批注，以提醒我们，该类必须可序列化，并仅保存显式标记为 JSON 属性的成员。
-  我们已使用 `[JsonProperty("name")]` 为要保存的字段做了批注，以提醒我们，某个字段是持久实体状态的一部分，并指定要在 JSON 表示形式中使用的属性名称。

但是，这些特性不是必需的；允许使用其他约定或特性，只要它们使用 Json.NET 即可。 例如，可以使用 `[DataContract]` 特性，或者不使用任何特性：

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

默认情况下，类的名称不会存储为 JSON 表示形式的一部分：即，我们将使用  *作为默认设置。* `TypeNameHandling.None` 可以使用 `JsonObject` 或 `JsonProperty` 特性来重写此默认行为。

### <a name="making-changes-to-class-definitions"></a>对类定义进行更改

在应用程序运行后对类定义进行更改时需要注意某些问题，因为存储的 JSON 对象可能不再与新的类定义相匹配。 尽管如此，只要用户了解 `JsonConvert.PopulateObject` 使用的反序列化过程，则通常就可以正确处理数据格式的更改。

例如，下面是一些更改的示例及其效果：

1. 如果添加了一个新属性，而该属性不在存储的 JSON 中，则系统假设该属性使用默认值。
1. 如果删除了一个不在存储的 JSON 中的属性，则以前的内容将会丢失。
1. 如果重命名某个属性，则效果如同删除旧属性并添加新属性。
1. 如果更改了某个属性的类型，使其不再可以从存储的 JSON 进行反序列化，则会引发异常。
1. 如果更改了某个属性的类型，但仍可以从存储的 JSON 将其反序列化，则会将其反序列化。

可以使用多个选项来自定义 Json.NET 的行为。 例如，若要在存储的 JSON 包含类中不存在的字段时强制引发异常，请指定 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` 特性。 还可以编写自定义的反序列化代码用于读取以任意格式存储的 JSON。

## <a name="entity-construction"></a>实体构造

有时，我们希望能够更好地控制实体对象的构造方式。 下面将会介绍几个用于在构造实体对象时更改默认行为的选项。 

### <a name="custom-initialization-on-first-access"></a>首次访问时的自定义初始化

在向从未访问过的或者已删除的实体调度操作之前，我们偶尔需要执行某种特殊的初始化。 若要指定此行为，可以在 `DispatchAsync` 的前面添加一个条件：

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>实体类中的绑定

与普通函数不同，实体类方法不能直接访问输入和输出绑定。 必须在入口点函数声明中捕获绑定数据，然后将其传递给 `DispatchAsync<T>` 方法。 传递给 `DispatchAsync<T>` 的任何对象将作为参数自动传入实体类构造函数。

以下示例演示如何将 `CloudBlobContainer`Blob 输入绑定[中的 ](../functions-bindings-storage-blob.md#input) 引用提供给基于类的实体使用。

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

有关 Azure Functions 中的绑定的详细信息，请参阅 [Azure Functions 触发器和绑定](../functions-triggers-bindings.md)文档。

### <a name="dependency-injection-in-entity-classes"></a>实体类中的依赖项注入

实体类支持 [Azure Functions 依赖项注入](../functions-dotnet-dependency-injection.md)。 以下示例演示如何将一个 `IHttpClientFactory` 服务注册到基于类的实体中。

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

以下代码片段演示如何将注入的服务合并到实体类中。

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 为了避免序列化问题，请务必排除要在序列化后存储注入值的字段。

> [!NOTE]
> 与在常规 .NET Azure Functions 中使用构造函数注入不同，必须将基于类的实体的函数入口点方法声明为 *。* `static` 声明非静态函数入口点可能导致正常的 Azure Functions 对象初始值设定项与持久实体对象初始值设定项之间发生冲突。

## <a name="function-based-syntax"></a>基于函数的语法

前面的内容侧重于基于类的语法，因为我们预期它更适用于大多数应用程序。 但是，对于想要定义或管理自身的实体状态和操作抽象的应用程序，可能适合使用基于函数的语法。 此外，在实现需要基于类的语法所不支持的泛型的库时，也可能适合使用该语法。 

使用基于函数的语法，实体函数可以显式处理操作调度和显式管理实体的状态。 例如，以下代码演示了使用基于函数的语法实现的 *Counter* 实体。  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>实体上下文对象

可以通过 `IDurableEntityContext` 类型的上下文对象访问特定于实体的功能。 此上下文对象可用作实体函数的参数，并可通过异步本地属性 `Entity.Current` 访问。

以下成员提供有关当前操作的信息，并允许指定返回值。 

* `EntityName`：当前正在执行的实体的名称。
* `EntityKey`：当前正在执行的实体的键。
* `EntityId`：当前正在执行的实体的 ID（包括名称和键）。
* `OperationName`：当前操作的名称。
* `GetInput<TInput>()`：获取当前操作的输入。
* `Return(arg)`：将值返回到调用该操作的业务流程。

以下成员管理实体的状态（创建、读取、更新、删除）。 

* `HasState`：实体是否存在，即，是否存在某种状态。 
* `GetState<TState>()`：获取实体的当前状态。 如果它尚不存在，则会创建它。
* `SetState(arg)`：创建或更新实体的状态。
* `DeleteState()`：删除实体的状态（如果存在）。 

如果 `GetState` 返回的状态是一个对象，可以通过应用程序代码直接修改它。 结束时无需再次调用 `SetState`（同时不会造成损害）。 如果多次调用 `GetState<TState>`，必须使用相同的类型。

最后，以下成员用于向其他实体发出信号，或启动新的业务流程：

* `SignalEntity(EntityId, operation, input)`：将单向消息发送到实体。
* `CreateNewOrchestration(orchestratorFunctionName, input)`：启动新的业务流程。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解实体的概念](durable-functions-entities.md)
