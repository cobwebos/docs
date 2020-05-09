---
title: 持久实体 - Azure Functions
description: 了解持久实体的概念，以及如何在 Azure Functions 的 Durable Functions 扩展中使用持久实体。
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4f45ac40e7df865bdb4722d086325096c377cd59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877536"
---
# <a name="entity-functions"></a>实体函数

实体函数定义读取和更新较小状态片段（称为“持久实体”）的操作。  与业务流程协调程序函数一样，实体函数是具有特殊触发器类型“实体触发器”的函数。  与业务流程协调程序函数不同，实体函数会显式管理实体状态，而不是通过控制流隐式表示状态。
实体提供了一种横向扩展应用程序的方式，即，将工作分散到多个实体，而每个实体具有适度大小的状态。

> [!NOTE]
> 实体函数和相关功能仅在 Durable Functions 2.0 及更高版本中可用。

## <a name="general-concepts"></a>一般概念

实体的行为有点类似于通过消息进行通信的微型服务。 每个实体具有唯一的标识和内部状态（如果存在）。 与服务或对象一样，实体会根据提示执行操作。 执行的操作可能会更新实体的内部状态。 它还可能会调用外部服务并等待响应。 实体使用通过可靠队列隐式发送的消息来与其他实体、业务流程和客户端通信。 

为了防止冲突，系统会保证针对单个实体的所有操作按顺序执行，即，一个接一个地执行。 

### <a name="entity-id"></a>实体 ID
可通过唯一标识符（实体 ID）访问实体。  实体 ID 只是用于唯一标识实体实例的一对字符串。 它包括：

* 实体名称，用于标识实体类型的名称  。 例如“Counter”。 此名称必须与实现该实体的实体函数的名称相匹配。 此名称不区分大小写。
* 实体键，用于在所有其他同名实体中唯一标识该实体的字符串  。 例如一个 GUID。

例如，可以使用 `Counter` 实体函数来保留在线游戏中的积分。 游戏的每个实例都具有唯一的实体 ID，例如 `@Counter@Game1` 和 `@Counter@Game2`。 针对特定实体的所有操作需要指定实体 ID 作为参数。

### <a name="entity-operations"></a>实体操作 ###

若要对实体调用操作，需指定：

* 目标实体的实体 ID  。
* 操作名称，用于指定要执行的操作的字符串。  例如，`Counter` 实体可以支持 `add`、`get` 或 `reset` 操作。
* 操作输入，操作的可选输入参数。  例如，add 操作可以采用整数数量作为输入。
* **计划时间**，这是用于指定操作交付时间的可选参数。 例如，可以可靠地计划一项操作在将来的几天运行。

操作可以返回结果值或错误结果，例如 JavaScript 错误或 .NET 异常。 调用操作的业务流程可以观察到此结果或错误。

实体操作还可以创建、读取、更新和删除实体的状态。 实体的状态始终持久保存在存储中。

## <a name="define-entities"></a>定义实体

目前，用于定义实体的两个不同的 API 为：

基于函数的语法，其中，实体以函数的形式表示，操作由应用程序显式调度  。 此语法适合用于具有简单状态、少量操作或一组动态操作的实体，例如在应用程序框架中。 此语法的维护可能很繁琐，因为它不会在编译时捕获类型错误。

**基于类的语法（仅限 .NET）** ，其中，实体和操作分别由类和方法表示。 此语法可生成更易于阅读的代码，使操作能够以类型安全的方式调用。 基于类的语法是建立基于函数的语法基础之上的一个精简层，因此，在同一应用程序中，这两种变体可以换用。

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>示例：基于函数的语法 - C#

以下代码是作为持久函数实现的简单 `Counter` 实体示例。 此函数定义三个操作：`add`、`reset` 和 `get`，每个操作针对整数状态运行。

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

有关基于函数的语法及其用法的详细信息，请参阅[基于函数的语法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="example-class-based-syntax---c"></a>示例：基于类的语法 - C#

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>示例：JavaScript 实体

从 `durable-functions` npm 包版本 1.3.0 开始，JavaScript 中提供了持久实体  。 以下代码是作为使用 JavaScript 编写的持久函数实现的 `Counter` 实体。

**Counter/function.json**
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

**Counter/index.js**
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

---

## <a name="access-entities"></a>访问实体

可以使用单向或双向通信访问实体。 以下术语用于区分这两种形式的通信： 

* 使用双向（往返）通信调用实体  。 向实体发送操作消息，然后等待响应消息，再继续。 响应消息可以提供结果值或错误结果，例如 JavaScript 错误或 .NET 异常。 然后，调用方会观察到此结果或错误。
* 使用单向（发后不理）通信向实体发出信号  。 发送操作消息，但不等待响应。 尽管可以保证消息最终会送达，但发送方并不知道何时送达，也无法观察到任何结果值或错误。

可以从客户端函数、业务流程协调程序函数或实体函数内部访问实体。 并非所有形式的通信都受所有上下文的支持：

* 在客户端内部，可以向实体发出信号，并可以读取实体状态。
* 在业务流程内部，可以向实体发出信号，并可以调用实体。
* 在实体内部，可以向实体发出信号。

以下示例演示了访问实体的各种方式。

### <a name="example-client-signals-an-entity"></a>示例：客户端向实体发出信号

若要从普通的 Azure 函数（也称为客户端函数）访问实体，请使用[实体客户端绑定](durable-functions-bindings.md#entity-client)。 以下示例演示一个队列触发的函数使用此绑定来发送实体信号。

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> 为简单起见，以下示例演示了用于访问实体的松散类型化语法。 通常，我们建议[通过接口访问实体](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因为这种方法提供更多的类型检查。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

术语“信号”是指实体 API 调用是单向、异步的。  客户端函数无法知道实体何时处理了操作。 另外，客户端函数无法观察到任何结果值或异常。 

### <a name="example-client-reads-an-entity-state"></a>示例：客户端读取实体状态

客户端函数还可以查询实体的状态，如以下示例中所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

实体状态查询将发送到持久跟踪存储，并返回实体的最近持久状态。 此状态始终为“已提交”，即，它永远不会是在执行操作的中途设想的暂时中间状态。 但是，与实体的内存中状态相比，此状态可能已过时。 如下一部分所述，只有业务流程可以读取实体的内存中状态。

### <a name="example-orchestration-signals-and-calls-an-entity"></a>示例：业务流程向实体发出信号和调用实体

业务流程协调程序函数可以使用[业务流程触发器绑定](durable-functions-bindings.md#orchestration-trigger)中的 API 访问实体。 以下示例代码演示了一个调用 `Counter` 实体并发送其信号的业务流程协调程序函数。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript 目前不支持从业务流程协调程序向实体发送信号。 请改用 `callEntity`。

---

只有业务流程能够调用实体和获取响应，该响应可能是返回值或异常。 使用[客户端绑定](durable-functions-bindings.md#entity-client)的客户端函数只能发送实体的信号。

> [!NOTE]
> 从业务流程协调程序函数调用实体类似于从业务流程协调程序函数调用[活动函数](durable-functions-types-features-overview.md#activity-functions)。 主要区别在于，实体函数是具有地址（实体 ID）的持久对象。 实体函数支持指定操作名称。 另外，活动函数是无状态的，没有操作概念。

### <a name="example-entity-signals-an-entity"></a>示例：实体向实体发出信号

当某个实体函数在执行操作时，可以向其他实体（甚至是自身）发送信号。
例如，我们可以修改上述 `Counter` 实体示例，以便在计数器达到值 100 时，向某个监视器实体发送“已达到里程碑”信号。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>实体协调（当前仅限 .NET）

有时可能需要跨多个实体协调操作。 例如，在银行应用程序中，可能会使用实体来代表不同的银行帐户。 将资金从一个帐户转移到另一个帐户时，必须确保源帐户有足够的资金。 还必须确保对源帐户和目标帐户的更新都以事务一致性的方式进行。

### <a name="example-transfer-funds-c"></a>示例：转移资金 (C#)

以下示例代码使用业务流程协调程序函数在两个 account 实体之间转移资金。 协调实体更新需要使用 `LockAsync` 方法在业务流程中创建一个关键的节。 

> [!NOTE]
> 为简单起见，本示例重复使用了前面定义的 `Counter` 实体。 在实际应用程序中，最好是定义更详细的 `BankAccount` 实体。

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

在上面的示例中，业务流程协调程序函数已将资金从源实体转到目标实体。 `LockAsync` 方法同时锁定了源和目标帐户实体。 这种锁定确保在业务流程逻辑退出位于 `using` 语句末尾的关键节之前，其他任何客户端都不能查询或修改任一帐户的状态。 此行为可防止从源帐户透支的情况。

> [!NOTE] 
> 当业务流程终止（正常终止，或终止并出错）时，正在进行的所有关键节都会隐式结束，并释放所有锁。

### <a name="critical-section-behavior"></a>关键节的行为

`LockAsync` 方法在业务流程中创建一个关键节。 这些关键节可防止其他业务流程对一组指定的实体进行重叠的更改。 在内部，`LockAsync` API 向实体发送“lock”操作；从每个相同实体收到“lock acquired”响应消息时，该 API 将会返回。 锁定和解锁是所有实体都支持的内置操作。

当实体处于锁定状态时，不允许其他客户端对其执行任何操作。 此行为确保每次只有一个业务流程实例可以锁定某个实体。 如果调用方尝试针对业务流程锁定的实体调用某个操作，该操作将被置于挂起操作队列中。 只有在持有锁的业务流程释放其锁之后，才会处理挂起的操作。

> [!NOTE] 
> 此行为与大多数编程语言中使用的同步基元（例如 C# 中的 `lock` 语句）略有不同。 例如，在 C# 中，所有线程必须使用 `lock` 语句来确保在多个线程之间正确同步。 但是，实体不需要所有调用方显式锁定实体。 如果任一调用方锁定实体，则针对该实体的所有其他操作将被阻止，并排队在该锁的后面。

实体中的锁是持久性的，因此，即使回收了执行进程，这些锁也仍会保留。 锁在内部保留为实体持久状态的一部分。

与事务不同，关键节在出错时不会自动回滚更改。 而是必须显式编写任何错误处理（例如，回滚或重试）的代码，例如，捕获错误或异常。 这是有意设计的。 一般情况下，很难或者根本无法自动回滚业务流程造成的所有影响，因为业务流程可能会运行活动，并调用无法回滚的外部服务。 此外，尝试回滚的过程本身可能会失败，需要进一步进行错误处理。

### <a name="critical-section-rules"></a>关键节规则

与大多数编程语言中的低级锁定基元不同，*不保证关键节死锁*。 为防止死锁，我们强制实施以下限制： 

* 关键节不可嵌套。
* 关键节无法创建子业务流程。
* 关键节只能调用它们锁定的实体。
* 关键节无法使用多个并行调用来调用同一实体。
* 关键节只能发送它们未锁定的实体的信号。

违反其中的任意规则都会导致运行时错误（例如 .NET 中的 `LockingRulesViolationException`），其中会提供一条消息来解释违反了哪条规则。

## <a name="comparison-with-virtual-actors"></a>与虚拟执行组件的比较

许多持久实体功能来源于[执行组件模型](https://en.wikipedia.org/wiki/Actor_model)的灵感。 如果你熟悉执行组件，则你可能会理解本文中所述的许多概念。 持久实体非常类似于[虚拟执行组件](https://research.microsoft.com/projects/orleans/)，或 [Orleans 项目](http://dotnet.github.io/orleans/)中普遍存在的“粒度”。 例如：

* 可通过实体 ID 对持久实体寻址。
* 持久实体操作按顺序逐个执行，以防止出现争用情况。
* 持久实体是在被调用或发送信号时隐式创建的。
* 不执行操作时，将以静默方式从内存中卸载持久实体。

有一些重要的差别值得注意：

* 持久实体优先考虑持久性而不是延迟，因此可能不适合用于延迟要求较为严格的应用程序。
* 持久实体不会对消息实施内置超时。 在 Orleans 中，所有消息会在可配置的时间后超时。 默认为 30 秒。
* 在实体之间发送的消息将按顺序可靠传送。 在 Orleans 中，通过流发送的内容支持可靠或有序传送，但不保证粒度之间发送的所有消息支持这种传送方式。
* 实体中的请求-响应模式限制为业务流程。 在实体内部，仅允许单向消息传送（也称为“发出信号”），原始执行组件模型中也是如此，但 Orleans 的粒度中不是如此。 
* 持久实体不会死锁。 在 Orleans 中，可能会发生死锁，并且在消息超时之前不会解决死锁。
* 持久实体可与持久业务流程结合使用，支持分布式锁定机制。 


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [阅读有关 .NET 中的持久实体的开发人员指南](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [了解任务中心](durable-functions-task-hubs.md)
