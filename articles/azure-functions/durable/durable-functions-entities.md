---
title: 持久实体 - Azure Functions
description: 了解持久实体的概念，以及如何在 Azure Functions 的 Durable Functions 扩展中使用持久实体。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 99e61cef55bd97704063e4d2da90909d0376c327
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961466"
---
# <a name="entity-functions-preview"></a>实体函数（预览版）

实体函数定义读取和更新较小状态片段（称为“持久实体”）的操作。  与业务流程协调程序函数一样，实体函数是具有特殊触发器类型“实体触发器”的函数。  与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

> [!NOTE]
> 实体函数和相关功能仅在 Durable Functions 2.0 及更高版本中可用。 实体函数目前以公共预览版提供。

## <a name="entity-identity"></a>实体标识

通过唯一标识符（“实体 ID”）访问实体（有时称为“实体实例”）。   实体 ID 只是用于唯一标识实体实例的一对字符串。 该环境包括：

* **实体名称**：用于标识实体类型的名称（例如“Counter”）。
* **实体键**：用于在所有其他同名实体中唯一标识该实体的字符串（例如一个 GUID）。

例如，可以使用 *counter* 实体函数来保留在线游戏中的积分。 游戏的每个实例具有唯一的实体 ID，例如 `@Counter@Game1`、`@Counter@Game2` 等。 针对特定实体的所有操作需要指定实体 ID 作为参数。

## <a name="programming-models"></a>编程模型

持久实体支持两种不同的编程模型。 第一种模型是一个动态的“功能”模型，其中的实体由单个函数定义。 第二种模型是面向对象的模型，其中的实体由类和方法定义。 后续部分将会介绍这些模型，以及用来与实体交互的编程模型。

### <a name="defining-entities"></a>定义实体

有两个可选的编程模型可用于创作持久实体。 以下代码是作为标准函数实现的简单 *Counter* 实体示例。 此函数定义三个操作：`add`、`reset` 和 `get`，每个操作针对整数状态值 `currentValue` 运行。 

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

此模型最适合用于简单的实体实现，或具有动态操作集的实现。 但是，也可以使用基于类的编程模型，该模型对于静态的但具有更复杂实现的实体非常有用。 以下示例是使用类和方法的 `Counter` 实体的等效实现。

```csharp
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

> [!NOTE]
> 使用实体类时，必须将具有 `[FunctionName]` 属性的函数入口点方法声明为 `static`。  非静态入口点方法可能会导致多次进行对象初始化，并可能导致其他不确定的行为。

在基于类的编程模型中，`IDurableEntityContext` 对象在 `Entity.Current` 静态属性中可用。

基于类的模型类似于 [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/) 普及化的编程模型。 在此模型中，实体类型定义为 .NET 类。 类的每个方法是可由外部客户端调用的操作。 不过，与 Orleans 不同，.NET 接口是可选的。 前面的 *Counter* 示例未使用接口，但仍可以通过其他函数或通过 HTTP API 调用来调用它。

> [!NOTE]
> 实体触发器函数在 Durable Functions 2.0 及更高版本中可用。 目前，实体触发器函数仅适用于 .NET 函数应用。

### <a name="accessing-entities-from-clients"></a>从客户端访问实体

可以从普通的函数（也称为“客户端函数”）使用[实体客户端输出绑定](durable-functions-bindings.md#entity-client)调用或查询持久实体。  以下示例演示一个队列触发的函数使用此绑定来发送实体信号。 

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

> [!NOTE]
> .NET 函数支持使用松散类型化的方法和类型安全的方法来发送实体信号。 有关详细信息，请参阅[实体客户端绑定](durable-functions-bindings.md#entity-client-usage)参考文档。

术语“信号”是指实体 API 调用是单向、异步的。  客户端函数无法知道实体何时处理了操作，而实体函数也无法将值返回给客户端函数。  基于队列的单向消息传送是一种有意的设计方式，使持久实体优先考虑持久性而不是性能。 此设计方式是持久实体相比于其他类似技术的折衷方案之一。 目前，如下一部分所述，只有业务流程能够处理实体的返回值。

客户端函数还可以查询实体的状态，如以下示例中所示：

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

实体状态查询将发送到持久跟踪存储，并返回实体的最近持久状态。  与实体的内存中状态相比，返回的状态可能已过时。 如下一部分所述，只有业务流程可以读取实体的内存中状态。

### <a name="accessing-entities-from-orchestrations"></a>从业务流程访问实体

业务流程协调程序函数可以使用[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)中的 API 访问实体。 业务流程协调程序函数可以在单向通信（“即发即弃”，也称为“信号发送”）与双向通信（“请求和响应”，也称为“调用”）之间进行选择。   以下示例代码演示了一个调用 *Counter* 实体并发送其信号的业务流程协调程序函数。  

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

只有业务流程能够调用实体和获取响应，该响应可能是返回值或异常。 使用[客户端绑定](durable-functions-bindings.md#entity-client)的客户端函数只能发送实体的信号。 

> [!NOTE]
> 从业务流程协调程序函数调用实体类似于从业务流程协调程序函数调用[活动函数](durable-functions-types-features-overview.md#activity-functions)。 主要区别在于，实体函数是具有地址（实体 ID）的持久对象，并支持指定操作名称。  另外，活动函数是无状态的，没有操作概念。

### <a name="dependency-injection-in-entity-classes-net"></a>实体类中的依赖项注入 (.NET)

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

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 与在常规 .NET Azure Functions 中使用构造函数注入不同，必须将基于类的实体的函数入口点方法声明为 `static`。  声明非静态函数入口点可能导致正常的 Azure Functions 对象初始值设定项与持久实体对象初始值设定项之间发生冲突。

### <a name="bindings-in-entity-classes-net"></a>实体类中的绑定 (.NET)

与普通函数不同，实体类方法不能直接访问输入和输出绑定。 必须在入口点函数声明中捕获绑定数据，然后将其传递给 `DispatchAsync<T>` 方法。 传递给 `DispatchAsync<T>` 的任何对象将作为参数自动传入实体类构造函数。

以下示例演示如何将 [Blob 输入绑定](../functions-bindings-storage-blob.md#input)中的 `CloudBlobContainer` 引用提供给基于类的实体使用。

```csharp
public class BlobBackedEntity
{
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

## <a name="entity-coordination"></a>实体协调

有时可能需要跨多个实体协调操作。 例如，在银行应用程序中，可能会使用实体来代表不同的银行帐户。 将资金从一个帐户转到另一个帐户时，必须确保源帐户具有足够的资金，并且对源和目标帐户的更新必须以事务一致的方式完成。   

### <a name="transfer-funds-example-in-c"></a>C# 中的资金转帐示例

以下示例代码使用业务流程协调程序函数在两个 _account_ 实体之间转移资金。 协调实体更新需要使用 `LockAsync` 方法在业务流程中创建一个关键的节： 

> [!NOTE]
> 为简单起见，本示例重复使用了前面定义的 `Counter` 实体。 但是，在实际应用程序中，最好是改为定义更详细的 `BankAccount` 实体。

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

在 .NET 中，`LockAsync` 在释放时会返回一个以关键节结尾的 `IDisposable`。 可将此 `IDisposable` 结果与 `using` 块一起使用，以获取关键节的语法表示形式。

在上面的示例中，业务流程协调程序函数已将资金从源实体转到目标实体。   `LockAsync` 方法同时锁定了源和目标帐户实体。   这种锁定确保在业务流程逻辑退出位于 `using` 语句末尾的关键节之前，其他任何客户端都不能查询或修改任一帐户的状态。  这可以有效地防止从源帐户透支的情况。 

### <a name="critical-section-behavior"></a>关键节的行为

`LockAsync` 方法在业务流程中创建一个关键节。  这些关键节可防止其他业务流程对一组指定的实体进行重叠的更改。  在内部，`LockAsync` API 向实体发送“lock”操作；从每个相同实体收到“lock acquired”响应消息时，该 API 将会返回。 *lock* 和 *unlock* 是所有实体都支持的内置操作。

当实体处于锁定状态时，不允许其他客户端对其执行任何操作。 此行为确保每次只有一个业务流程实例可以锁定某个实体。 如果调用方尝试针对业务流程锁定的实体调用某个操作，该操作将被置于挂起操作队列中。  只有在持有锁的业务流程释放其锁之后，才会处理挂起的操作。

> [!NOTE] 
> 这与大多数编程语言中使用的同步基元（例如 C# 中的 `lock` 语句）略有不同。 例如，在 C# 中，所有线程必须使用 `lock` 语句来确保在多个线程之间正确同步。 但是，实体不需要所有调用方显式锁定实体。  如果任一调用方锁定实体，则针对该实体的所有其他操作将被阻止，并排队在该锁的后面。

实体中的锁是持久性的，因此，即使回收了执行进程，这些锁也仍会保留。 锁在内部保留为实体持久状态的一部分。

### <a name="critical-section-restrictions"></a>关键节的限制

我们对关键节的使用方式施加了一些限制。 这些限制旨在防止死锁和重入。

* 关键节不可嵌套。
* 关键节无法创建子业务流程。
* 关键节只能调用它们锁定的实体。
* 关键节无法使用多个并行调用来调用同一实体。
* 关键节只能发送它们未锁定的实体的信号。

## <a name="comparison-with-virtual-actors"></a>与虚拟执行组件的比较

许多持久实体功能来源于[执行组件模型](https://en.wikipedia.org/wiki/Actor_model)的灵感。 如果你熟悉执行组件，则你可能会理解本文中所述的许多概念。 具体而言，持久实体在多个方面类似于[虚拟执行组件](https://research.microsoft.com/projects/orleans/)：

* 可通过实体 ID 对持久实体寻址。 
* 持久实体操作按顺序逐个执行，以防止出现争用情况。
* 持久实体是在被调用或发送信号时自动创建的。
* 不执行操作时，将以静默方式从内存中卸载持久实体。

不过，有一些重要的差别值得注意：

* 持久实体优先考虑持久性而不是延迟，因此可能不适合用于延迟要求较为严格的应用程序。  
* 在实体之间发送的消息将按顺序可靠传送。
* 持久实体可与持久业务流程结合使用，支持分布式锁定机制。
* 实体中的请求/响应模式限制为业务流程。 对于客户端到实体以及实体到实体的通信，只允许单向消息传送（也称为“信号发送”），就像在原始执行组件模型中一样。   此行为可防止分布式死锁。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解任务中心](durable-functions-task-hubs.md)