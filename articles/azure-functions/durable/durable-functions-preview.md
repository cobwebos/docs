---
title: Durable Functions 预览版功能 - Azure Functions
description: 了解 Durable Functions 的预览版功能。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 7356541ed6288603a66d5caa43138284d8d4d918
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320470"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 预览版 (Azure Functions)

Durable Functions 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的扩展，可用于在无服务器环境中编写有状态函数  。 该扩展可用于管理状态、检查点和重启。 如果你不熟悉 Durable Functions，请参阅[概述文档](durable-functions-overview.md)。

Durable Functions 1.x 是 Azure Functions 的 GA (正式发布) 功能, 但也包含多个目前以公共预览版提供的子功能。 本文介绍最新发布的预览版功能，并详细介绍其工作原理和用法。

> [!NOTE]
> 这些预览功能是 Durable Functions 2.0 版本的一部分, 当前是一种包含多项重大更改的**预览质量版本**。 可以在 nuget.org 上找到具有**2.0.0-betaX**形式版本的持久扩展包生成。 Azure Functions 这些版本不适用于生产工作负荷, 后续版本可能包含其他重大更改。

## <a name="breaking-changes"></a>重大变化

Durable Functions 2.0 中引入了几项中断性变更。 现有的应用程序在不更改代码的情况下预期不会与 Durable Functions 2.0 兼容。 本部分列出了其中的部分更改：

### <a name="hostjson-schema"></a>Host.json 架构

以下代码片段演示了 host.json 的新架构。 要注意的主要变化是新的小节:

* `"storageProvider"`存储特定配置`"azureStorage"`的 (和子节)
* `"tracking"`用于跟踪和日志记录配置
* `"notifications"`事件网格通知`"eventGrid"`配置的 (和子节)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

随着 Durable Functions 2.0 的不断稳定，host.json `durableTask` 节中会引入更多的更改。 有关这些更改的详细信息，请参阅[此 GitHub 问题](https://github.com/Azure/azure-functions-durable-extension/issues/641)。

### <a name="public-interface-changes"></a>公共接口更改

Durable Functions 支持的各种“上下文”对象包含适合在单元测试中使用的抽象基类。 作为 Durable Functions 2.0 的一部分，这些抽象基类已由接口取代。 直接使用具体类型的函数代码不受影响。

下表显示了主要更改：

| 旧类型 | 新类型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

如果抽象基类包含虚拟方法，则这些虚拟方法已由 `DurableContextExtensions` 中定义的扩展方法取代。

## <a name="entity-functions"></a>实体函数

实体函数定义用于读取和更新较小状态片段（称为“持久实体”）的操作。  与业务流程协调程序函数类似，实体函数是具有特殊触发器类型“实体触发器”的函数。  与业务流程协调程序函数不同，实体函数没有任何特定的代码约束。 实体函数还会显式管理状态，而不是通过控制流隐式表示状态。

### <a name="net-programing-models"></a>.NET 编程模型

有两个用于创作持久性实体的可选编程模型。 下面的代码是作为标准函数实现的简单*计数器*实体的示例。 此函数定义三个操作`add`: `reset`、和`get`, `currentValue`每个*操作*都操作一个整数状态值。

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

此模型最适用于简单实体实现或具有动态操作集的实现。 但是, 也有一个基于类的编程模型, 它对于静态但具有更复杂的实现的实体很有用。 下面的示例是使用 .net 类和方法`Counter`的实体的等效实现。

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

基于类的模型类似于那时推广的编程[模型。](https://www.microsoft.com/research/project/orleans-virtual-actors/) 在此模型中, 实体类型定义为 .NET 类。 类的每个方法都是可由外部客户端调用的操作。 不过, 与奥尔良不同, .NET 接口是可选的。 前面的*计数器*示例未使用接口, 但仍可通过其他函数或 HTTP API 调用来调用它。

通过唯一的标识符（实体 ID）访问实体实例。   实体 ID 只是用于唯一标识实体实例的字符串对。 该环境包括：

* **实体名称**: 标识实体类型的名称 (例如, "计数器")。
* **实体键**: 唯一标识同一名称的所有其他实体中的实体 (例如, GUID) 的字符串。

例如，*counter* 实体函数可用于保留在线游戏中的积分。 游戏的每个实例都有一个唯一的实体 ID，例如 `@Counter@Game1`、`@Counter@Game2`，等等。

### <a name="comparison-with-virtual-actors"></a>与虚拟执行组件的比较

持久实体的设计在很大程度上受[执行组件模型](https://en.wikipedia.org/wiki/Actor_model)的影响。 如果你熟悉执行组件，则应该也熟悉持久实体相关的概念。 具体而言，持久实体在很多方面类似于[虚拟执行组件](https://research.microsoft.com/projects/orleans/)：

* 可通过实体 ID 对持久实体进行寻址。 
* 持久实体操作按顺序执行，每次只执行一个，以防止出现争用状态。
* 调用持久实体或向其发出信号时，会自动创建持久实体。
* 未执行操作时，持久实体将以静默方式从内存中卸载。

但是，有一些值得注意的重要差别：

* 持久实体优先考虑持久性而不是延迟，因此，它们可能不适合用于需要满足严格延迟要求的应用程序。  
* 在实体之间发送的消息将按顺序可靠传送。
* 持久实体可与持久业务流程结合使用，可充当本文稍后将会介绍的分布式锁。
* 实体中的请求/响应模式限制为业务流程。 对于实体间的通信，与原始执行组件模型中一样，只允许单向消息（也称为“信号”）。 此行为可防止分布式死锁。

### <a name="durable-entity-net-apis"></a>持久性实体 .NET Api

实体支持涉及到多个 API。 例如，有一个新的 API 可以定义实体函数（如上所示），这些函数指定对某个实体调用某个操作时会发生什么情况。 此外，已使用新功能更新了客户端和业务流程的现有 API，以便与实体交互。

#### <a name="implementing-entity-operations"></a>实现实体操作

针对实体执行操作可以针对上下文对象（.NET 中的 `IDurableEntityContext`）调用这些成员：

* **OperationName**：获取操作的名称。
* **Getinputt>\<TInput >** : 获取操作的输入。
* **GetState\<TState >** : 获取实体的当前状态。
* **SetState**：更新实体的状态。
* **SignalEntity**：将单向消息发送到实体。
* **Self**：获取实体的 ID。
* **Return**：将值返回到调用该操作的客户端或业务流程。
* **IsNewlyConstructed**：如果在调用该操作之前该实体不存在，则返回 `true`。
* **DestructOnExit**：完成操作后删除实体。

操作的限制性比业务流程更低：

* 操作可以使用同步或异步 API 调用外部 I/O（我们建议仅使用异步 API）。
* 操作可以是不确定性的。 例如，可以安全调用 `DateTime.UtcNow`、`Guid.NewGuid()` 或 `new Random()`。

#### <a name="accessing-entities-from-clients"></a>从客户端访问实体

可以通过 `orchestrationClient` 绑定（.NET 中的 `IDurableOrchestrationClient`）从普通函数调用持久实体。 支持以下方法：

* **ReadEntityStateAsync\<T>** ：读取实体的状态。
* **SignalEntityAsync**：将单向消息发送到实体，并等待消息排队。
* **SignalEntityAsync\<T >** : 与相同`SignalEntityAsync` , 但使用类型`T`的生成的代理对象。

之前`SignalEntityAsync`的调用要求将实体操作的名称指定`string`为, 并将操作的负载指定为`object`。 下面的示例代码是此模式的示例:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

还可以为类型安全访问生成代理对象。 若要生成类型安全的代理, 实体类型必须实现接口。 例如, 假设前面提到`Counter`的实体实现了`ICounter`接口, 定义如下:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

然后, 客户端代码`SignalEntityAsync<T>`可以使用并`ICounter`将接口指定为类型参数, 以生成类型安全的代理。 下面的代码示例演示了如何使用类型安全的代理:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

在上面的示例中, `proxy`参数是动态生成的`ICounter`实例, 它`Add`在内部将对的调用转换为对`SignalEntityAsync`的等效 (非类型化) 调用。

> [!NOTE]
> 请务必注意`ReadEntityStateAsync` , 和`SignalEntityAsync`方法`IDurableOrchestrationClient`优先于性能。 `ReadEntityStateAsync`可以返回过期值, 并`SignalEntityAsync`可在操作完成之前返回。

#### <a name="accessing-entities-from-orchestrations"></a>从业务流程访问实体

业务流程可以使用`IDurableOrchestrationContext`对象来访问实体。 它们可以在单向通信（即发即弃）与双向通信（请求和响应）之间做出选择。 各自的方法是:

* **SignalEntity**：将单向消息发送到实体。
* **CallEntityAsync**：将消息发送到实体，并等待指示操作完成的响应。
* **CallEntityAsync\<T>** ：将消息发送到实体，并等待包含 T 类型的结果的响应。

使用双向通信时，在执行操作期间引发的任何异常也会传回到调用方业务流程，并重新引发。 与此相反，使用即发即弃通信模式时，观测不到异常。

对于类型安全访问, 业务流程函数可以基于接口生成代理。 `CreateEntityProxy`扩展方法可用于此目的:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

在前面的示例中, 假定存在一个实现`IAsyncCounter`接口的 "计数器" 实体。 然后, 业务流程就可以使用`IAsyncCounter`类型定义生成代理类型以同步与实体交互。

### <a name="locking-entities-from-orchestrations"></a>从业务流程锁定实体

业务流程可以锁定实体。 借助此功能可以方便地使用关键节来防止不利的争用状态。 

上下文对象提供以下方法：

* **LockAsync**：获取一个或多个实体中的锁。
* **IsLocked**：如果当前位于关键节中，则返回 true，否则返回 false。

当业务流程结束时，关键节将会结束，所有锁将会释放。 在.NET 中，`LockAsync` 返回释放时结束关键节的 `IDisposable`，可将它与 `using` 子句结合使用，以获取关键节的语法表示形式。

例如，假设某个业务流程需要测试两个玩家是否有空，然后为他们各分配一个游戏。 可按如下所示使用关键节来完成此任务：

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

在关键节中，这两个玩家实体都已锁定，这意味着，他们只执行了从关键节内部调用的操作，而未执行任何其他操作。 此行为可防止有冲突的操作（例如，将玩家分配到其他游戏，或注销）出现争用。

我们对关键节的用法施加了多种限制。 这些限制旨在防止死锁和可重入性。

* 无法嵌套关键节。
* 关键节无法创建子业务流程。
* 关键节只能调用它们锁定的实体。
* 关键节无法使用多个并行调用来调用同一个实体。
* 关键节只能向它们未锁定的实体发出信号。

## <a name="alternate-storage-providers"></a>备用的存储提供程序

Durable Task Framework 目前支持多个存储提供程序，包括 [Azure 存储](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、[Azure 服务总线](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[内存中仿真器](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)和试验性的 [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) 提供程序。 但是，到目前为止，适用于 Azure Functions 的 Durable Task 扩展仅支持 Azure 存储提供程序。 从 Durable Functions 2.0 开始，即将添加对备用存储提供程序的支持（首先是 Redis 提供程序）。

> [!NOTE]
> Durable Functions 2.0 仅支持与 .NET Standard 2.0 兼容的提供程序。 在撰写本文时，Azure 服务总线提供程序不支持 .NET Standard 2.0，因此不可用作备用的存储提供程序。

### <a name="emulator"></a>仿真器

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) 提供程序是适合用于本地测试方案的本地内存非持久性存储提供程序。 可以使用以下精简的 **host.json** 架构对其进行配置：

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis（试验性）

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) 提供程序将所有业务流程状态保存到配置的 Redis 群集中。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` 必须引用应用设置或环境变量的名称。 该应用设置或环境变量应包含“服务器:端口”格式的 Redis 连接字符串值。  例如，用于连接本地 Redis 群集的 `localhost:6379`。

> [!NOTE]
> Redis 提供程序目前是试验性的，仅支持单个节点上运行的函数应用。 不保证 Redis 提供程序已正式发布, 并且可能会在将来的版本中删除。
