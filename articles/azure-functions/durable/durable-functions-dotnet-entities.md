---
title: .NET 中持久实体的开发人员指南-Azure Functions
description: 如何使用 Azure Functions 的 Durable Functions 扩展处理 .NET 中的持久实体。
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 750ccbfa885b4679dfa61240b49ea9ec86a46d51
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120635"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET 中持久实体的开发人员指南

本文介绍如何使用 .NET 开发持久性实体的可用接口，包括示例和一般建议。 

实体函数为无服务器应用程序开发人员提供了一种方便的方式，将应用程序状态组织为细化实体的集合。 有关基础概念的更多详细信息，请参阅 "[持久实体：概念](durable-functions-entities.md)" 一文。

我们目前提供了两个用于定义实体的 Api：

- **基于类的语法**将实体和操作表示为类和方法。 此语法可轻松编写代码，并允许通过接口以类型检查的方式调用操作。 

- **基于函数的语法**是将实体表示为函数的较低级别接口。 它可精确控制如何分派实体操作以及实体状态的管理方式。  

本文主要侧重于基于类的语法，因为我们希望它更适用于大多数应用程序。 但是，[基于函数的语法](#function-based-syntax)可能适用于想要为实体状态和操作定义或管理自己的抽象的应用程序。 此外，它可能适用于实现需要 genericity 的库，但目前不支持基于类的语法。 

> [!NOTE]
> 基于类的语法只是基于函数的语法的一层，因此可以在同一应用程序中同时使用这两种变体。 
 
## <a name="defining-entity-classes"></a>定义实体类

下面的示例是一个 `Counter` 实体的实现，该实体存储类型为 integer 的单个值，并提供四种操作 `Add`、`Reset`、`Get`和 `Delete`。

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

`Run` 函数包含使用基于类的语法所需的样板。 它必须是*静态*Azure 函数。 对于由实体处理的每个操作消息，它将执行一次。 当调用 `DispatchAsync<T>` 并且实体不在内存中时，它将构造一个类型的对象 `T`，并从存储中的最后一个持久 JSON （如果有）填充其字段。 然后，它调用具有匹配名称的方法。

> [!NOTE]
> 基于类的实体的状态是在实体处理操作之前**隐式创建**的，并且可以通过调用 `Entity.Current.DeleteState()`在操作中**显式删除**。

### <a name="class-requirements"></a>类要求
 
实体类是不需要特殊的超类、接口或属性的 Poco （普通旧 CLR 对象）。 但是：

- 类必须是可构造（请参阅[实体构造](#entity-construction)）。
- 该类必须是 JSON 可序列化的（请参阅[实体序列化](#entity-serialization)）。

此外，任何旨在作为操作调用的方法都必须满足其他要求：

- 操作最多只能有一个自变量，并且不能有任何重载或泛型类型参数。
- 要从使用接口的业务流程调用的操作必须返回 `Task` 或 `Task<T>`。
- 参数和返回值必须是可序列化的值或对象。

### <a name="what-can-operations-do"></a>操作可以执行哪些操作？

所有实体操作都可以读取和更新实体状态，对状态所做的更改会自动保存到存储中。 此外，操作可以在所有 Azure Functions 通用限制范围内执行外部 i/o 或其他计算。

操作还可以访问 `Entity.Current` 上下文提供的功能：

* `EntityName`：当前正在执行的实体的名称。
* `EntityKey`：当前正在执行的实体的键。
* `EntityId`：当前正在执行的实体（包括名称和密钥）的 ID。
* `SignalEntity`：向实体发送单向消息。
* `CreateNewOrchestration`：启动新的业务流程。
* `DeleteState`：删除此实体的状态。

例如，我们可以修改计数器实体，使其在计数器达到100时启动业务流程，并将实体 ID 作为输入参数传递：

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

可以使用实体的显式字符串名称及其操作直接访问基于类的实体。 我们提供以下示例：有关基础概念的更深入说明（如信号与调用），请参阅[访问实体](durable-functions-entities.md#access-entities)中的讨论。 

> [!NOTE]
> 如果可能，我们建议[通过接口访问实体](#accessing-entities-through-interfaces)，因为它提供更多的类型检查。

### <a name="example-client-signals-entity"></a>示例：客户端信号实体

以下 Azure Http 函数使用 REST 约定实现删除操作。 它将一个删除信号发送到计数器实体，该实体的键传入 URL 路径。

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

以下 Azure Http 函数使用 REST 约定实现 GET 操作。 它读取其键在 URL 路径中传递的计数器实体的当前状态。

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
> `ReadEntityStateAsync` 返回的对象只是一个本地副本，即某个较早时间点的实体状态的快照。 特别是，它可能已过时，并且修改此对象不会影响实际的实体。 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>示例：业务流程第一个信号，然后调用实体

下面的业务流程向计数器实体发出信号，以将其递增，然后调用相同的实体来读取其最新值。

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

接口可用于通过生成的代理对象访问实体。 此方法确保操作的名称和参数类型与实现的内容匹配。 建议尽可能使用接口访问实体。

例如，我们可以修改计数器示例，如下所示：

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

实体类和实体接口类似于[奥尔良](https://www.microsoft.com/research/project/orleans-virtual-actors/)那时推广的粒度和颗粒接口。 有关持久性实体与奥尔良之间的相似性和差异的详细信息，请参阅[与虚拟参与者比较](durable-functions-entities.md#comparison-with-virtual-actors)。

除了提供类型检查以外，接口对于更好地分离应用程序内的问题很有用。 例如，由于一个实体可以实现多个接口，因此一个实体可以为多个角色提供服务。 此外，由于接口可能由多个实体实现，因此可以将常规通信模式实现为可重复使用的库。

### <a name="example-client-signals-entity-through-interface"></a>示例：客户端信号实体到接口

客户端代码可以使用 `SignalEntityAsync<TEntityInterface>` 将信号发送到实现 `TEntityInterface`的实体。 例如：

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

在此示例中，`proxy` 参数是动态生成的 `ICounter`实例，它在内部将对 `Delete` 的调用转换为信号。

> [!NOTE]
> `SignalEntityAsync` Api 只能用于单向操作。 即使操作返回 `Task<T>`，`T` 参数的值也将始终为 null 或 `default`，而不是实际结果。
例如，如果不返回任何值，则表示 `Get` 操作发出信号是有意义的。 相反，客户端可以使用 `ReadStateAsync` 直接访问计数器状态，也可以启动调用 `Get` 操作的业务流程协调程序函数。 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>示例：业务流程优先发出信号，然后通过代理调用实体

若要从业务流程内部调用或发送实体，可以使用 `CreateEntityProxy` 和接口类型，为实体生成代理。 然后，可以使用此代理来调用或信号操作：

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

任何返回 `void` 的操作都将被终止，并会调用返回 `Task` 或 `Task<T>` 的任何操作。 可以通过显式使用 `SignalEntity<IInterfaceType>` 方法来更改此默认行为以及信号操作（即使它们返回任务）。

### <a name="shorter-option-for-specifying-the-target"></a>用于指定目标的更短选项

使用接口对实体进行调用或信号传输时，第一个参数必须指定目标实体。 可以通过指定实体 ID 指定目标，或者在仅有一个实现实体的类的情况下指定目标，只是实体键：

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

如果只指定了实体键，并且在运行时找不到唯一的实现，则会引发 `InvalidOperationException`。 

### <a name="restrictions-on-entity-interfaces"></a>实体接口限制

与往常一样，所有参数和返回类型都必须是 JSON 可序列化的。 否则，在运行时将引发序列化异常。

还需要执行一些其他规则：
* 实体接口必须仅定义方法。
* 实体接口不得包含泛型参数。
* 实体接口方法不能有多个参数。
* 实体接口方法必须返回 `void`、`Task`或 `Task<T>` 

如果违反了这些规则中的任何规则，则在运行时将在运行时引发 `InvalidOperationException`，前提是该接口用作 `SignalEntity` 或 `CreateProxy`的类型参数。 异常消息说明哪个规则已断开。

> [!NOTE]
> 返回 `void` 的接口方法只能有信号（单向），未被调用（双向）。 返回 `Task` 或 `Task<T>` 的接口方法可以调用，也可以进行信号。 如果调用，它们将返回操作的结果，或重新引发由操作引发的异常。 但是，当发出信号时，它们不会从操作返回实际结果或异常，而只返回默认值。

## <a name="entity-serialization"></a>实体序列化

因为实体的状态是持久持久的，所以 entity 类必须是可序列化的。 Durable Functions 运行时使用[Json.NET](https://www.newtonsoft.com/json)库来实现此目的，该库支持大量策略和属性以控制序列化和反序列化过程。 最常用的C#数据类型（包括数组和集合类型）已可序列化，可轻松用于定义持久性实体的状态。

例如，Json.NET 可以轻松地对以下类进行序列化和反序列化：

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

### <a name="serialization-attributes"></a>序列化属性

在上面的示例中，我们选择包含多个属性，使基础序列化更可见：
- 我们用 `[JsonObject(MemberSerialization.OptIn)]` 批注类，以便提醒我们，该类必须是可序列化的，并且只保存显式标记为 JSON 属性的成员。
-  我们使用 `[JsonProperty("name")]` 来批注要保存的字段，以便提醒我们字段是持久化实体状态的一部分，并指定要在 JSON 表示形式中使用的属性名称。

但这些属性不是必需的;当使用 Json.NET 时，可以使用其他约定或属性。 例如，一个可以使用 `[DataContract]` 特性，或者根本不使用特性：

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

默认情况下，*不*会将类的名称存储为 JSON 表示形式的一部分：也就是说，我们使用 `TypeNameHandling.None` 作为默认设置。 使用 `JsonObject` 或 `JsonProperty` 属性可以重写此默认行为。

### <a name="making-changes-to-class-definitions"></a>对类定义进行更改

在应用程序运行后对类定义进行更改时需要注意，因为存储的 JSON 对象可能不再与新的类定义匹配。 尽管如此，只要一种方法了解 `JsonConvert.PopulateObject`所使用的反序列化过程，通常可以正确地处理数据格式的变化。

例如，下面是一些更改的示例及其效果：

1. 如果添加了新的属性，而该属性不在存储的 JSON 中，则假定其默认值。
1. 如果删除了存储的 JSON 中的属性，则以前的内容将丢失。
1. 如果重命名某个属性，则其效果就像删除旧的属性并添加一个新属性一样。
1. 如果更改了某个属性的类型，使其无法再从存储的 JSON 进行反序列化，则会引发异常。
1. 如果属性的类型发生更改，但仍可从存储的 JSON 进行反序列化，则会这样做。

有很多选项可用于自定义 Json.NET 的行为。 例如，若要在存储的 JSON 包含类中不存在的字段时强制执行异常，请指定 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`特性。 还可以编写反序列化的自定义代码，该代码可读取以任意格式存储的 JSON。

## <a name="entity-construction"></a>实体构造

有时，我们想要更好地控制如何构造实体对象。 现在，我们介绍了几个用于更改构造实体对象时的默认行为的选项。 

### <a name="custom-initialization-on-first-access"></a>首次访问时的自定义初始化

偶尔，我们需要在向从未访问过或已被删除的实体调度操作之前，执行一些特殊的初始化操作。 若要指定此行为，可以在 `DispatchAsync`之前添加条件：

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>实体类中的绑定

与常规函数不同，entity 类方法不能直接访问输入和输出绑定。 必须在入口点函数声明中捕获绑定数据，然后将其传递给 `DispatchAsync<T>` 方法。 传递给 `DispatchAsync<T>` 的任何对象将作为参数自动传入实体类构造函数。

以下示例演示如何将 [Blob 输入绑定](../functions-bindings-storage-blob.md#input)中的 `CloudBlobContainer` 引用提供给基于类的实体使用。

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
> 若要避免序列化问题，请确保从序列化中排除旨在存储注入值的字段。

> [!NOTE]
> 与在常规 .NET Azure Functions 中使用构造函数注入不同，必须将基于类的实体的函数入口点方法声明为 `static`。 声明非静态函数入口点可能导致正常的 Azure Functions 对象初始值设定项与持久实体对象初始值设定项之间发生冲突。

## <a name="function-based-syntax"></a>基于函数的语法

到目前为止，我们将重点放在基于类的语法上，因为我们希望它更适用于大多数应用程序。 但是，基于函数的语法适用于想要为实体状态和操作定义或管理其各自抽象的应用程序。 此外，在实现需要 genericity 的库（目前不受基于类的语法支持）时，可能会适合使用此方法。 

利用基于函数的语法，实体函数显式处理操作调度，并显式管理实体的状态。 例如，下面的代码演示了使用基于函数的语法实现的*计数器*实体。  

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

可以通过 `IDurableEntityContext`类型的上下文对象访问特定于实体的功能。 此上下文对象作为实体函数的参数提供，并通过 async-local 属性 `Entity.Current`提供。

以下成员提供当前操作的相关信息，并允许我们指定返回值。 

* `EntityName`：当前正在执行的实体的名称。
* `EntityKey`：当前正在执行的实体的键。
* `EntityId`：当前正在执行的实体（包括名称和密钥）的 ID。
* `OperationName`：当前操作的名称。
* `GetInput<TInput>()`：获取当前操作的输入。
* `Return(arg)`：将值返回到调用操作的业务流程。

以下成员管理实体（创建、读取、更新、删除）的状态。 

* `HasState`：实体是否存在，即是否存在某种状态。 
* `GetState<TState>()`：获取实体的当前状态。 如果它尚不存在，则创建它。
* `SetState(arg)`：创建或更新实体的状态。
* `DeleteState()`：删除实体的状态（如果存在）。 

如果 `GetState` 返回的状态是一个对象，则它可以由应用程序代码直接修改。 结束时无需再次调用 `SetState` （但也不会造成危害）。 如果多次调用 `GetState<TState>`，则必须使用相同的类型。

最后，使用以下成员通知其他实体，或启动新的业务流程：

* `SignalEntity(EntityId, operation, input)`：向实体发送单向消息。
* `CreateNewOrchestration(orchestratorFunctionName, input)`：启动新的业务流程。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解实体概念](durable-functions-entities.md)
