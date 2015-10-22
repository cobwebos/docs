<properties
   pageTitle="Azure Service Fabric 参与者资源调控设计模式"
   description="有关可以如何使用 Service Fabric 参与者对需要缩放但使用受约束资源的应用程序进行建模的设计模式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/11/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：资源调控
此模式和相关方案可被在本地或云中具有受约束资源（无法立即扩展的资源）或要向云发送大规模应用程序和数据的开发人员（企业或其他方面）轻松识别。

在企业中，这些受约束资源（如数据库）在扩展的硬件上运行。具有较长企业历史记录的任何人都知道这是本地的常见情况。即使在云规模上，当云服务尝试超过地址/端口元组之间连接的 64K TCP 限制时，或尝试连接到限制并发连接数的基于云的数据库时，我们会看到此情况发生。

在过去，通常通过基于消息的中间件或自定义构建池和外观机制进行限制，来解决此问题。这些方法难以正确进行，特别是当我们需要缩放中间层，但仍保持正确的连接计数时。只是比较脆弱且复杂。

实际上，与智能缓存模式一样，此模式跨越多个方案以及已具有包含受约束资源的工作系统的客户。它们在构建这样的系统，其中不仅需要扩大服务，而且需要扩大内存中的状态和稳定存储中的持久化状态。

下图说明了此方案：

![][1]

## 使用参与者的缓存建模方案
实质上，我们是以充当某个资源或一组资源的代理（例如连接）的一个或多个参与者的形式对资源访问进行建模。你随后可以通过各个参与者直接管理资源，或使用管理资源参与者的协调参与者。为了使这一点更具体，我们将解决由于性能和可伸缩性原因而必须针对分区（也称为共享）存储层进行工作这一常见需要。我们的第一个选项非常基本：我们可以使用一个静态函数将参与者映射并解析到下游资源。例如，这类函数可以返回具有给定输入的连接字符串。如何实现该函数完全取决于我们。当然，此方法具有自己的缺点，例如使重新分区资源或将参与者重新映射到资源非常困难的静态关联。下面是一个非常简单的示例 — 我们执行取模算术运算来使用 userId 确定数据库名称并使用 region 来标识数据库服务器。

## 资源调控代码示例 – 静态解析

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

简单，但并不非常灵活。现在让我们来看一看更加高级且有用的方法。首先，我们对物理资源和参与者之间的关联进行建模。这看通过一个名为 Resolver 的参与者来实现，它了解用户、逻辑分区与物理资源之间的映射。Resolver 在持久存储中维护其数据，不过它会进行缓存以便于查找。正如我们前面在智能缓存模式的汇率示例中所看到的那样，Resolver 可以使用计时器主动提取最新信息。User 参与者解析了它需要使用的资源之后，会将它缓存在一个名为 \_resolution 的本地变量中，并在其生存期内使用它。我们对简单哈希或范围哈希选择了基于查找的解析（如下所示），因为它可在诸如缩小/扩大或将用户从一个资源移动到另一个资源的这类操作中提供灵活性。

![][2]

在上图中，我们看到参与者 B23 先解析其资源（即 resolution）— DB1 并缓存它。后续操作现在可以使用缓存的 resolution 来访问受约束资源。由于参与者支持单线程执行，因此开发人员不再需要担心对资源进行的并发访问。User 和 Resolver 参与者如下所示：

## 资源调控代码示例 – Resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : Actor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

资源调控 – Resolver 示例

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : Actor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## 通过有限功能访问资源
现在让我们来看看另一个示例；通过有限吞吐量功能对宝贵资源（如数据库、存储帐户和文件系统）进行独占访问。我们的方案如下所示：我们要使用一个名为 EventProcessor 的参与者处理事件，该参与者负责处理并保持事件（在此例中保持到 .CSV 文件以便进行简化）。虽然我们可以按照前面讨论的分区方法扩大我们的资源，不过我们仍需要处理并发问题。这便是为何我们选择基于文件的示例来阐释这一特定事项（从多个参与者向单个文件写入会引发并发问题）的原因。为了解决该问题，我们引入了另一个名为 EventWriter 的参与者，它具有受约束资源的独占所有权。该方案如下所示：

![][3]

我们将 EventProcessor 参与者标记为“无状态辅助角色”，它允许运行时根据需要在群集间缩放它们。因此我们没有为这些参与者使用上图中的任何标识符。换句话说，无状态参与者是由运行时维护辅助角色池。在下面的示例代码中，EventProcessor 参与者执行两项操作：它首先决定要使用的 EventWriter（资源），并调用所选参与者以编写处理的事件。为简单起见，我们为 EventWriter 参与者选择事件类型作为标识符。换而言之，有一个且只有一个 EventWriter 用于此事件类型，从而提供对资源的单线程独占访问。

## 资源调控代码示例 – 事件处理器

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

现在让我们来看一下 EventWriter 参与者。除了控制对受约束资源的独占访问（在此例中是文件以及对它进行的写入事件）之外，它实际上并不执行很多操作。
## 资源调控代码示例 – 事件写入器

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

让单个参与者负责资源允许我们添加功能，如缓冲。我们可以缓冲传入事件，并使用计时器定期或是在我们的缓冲区已满时写入这些事件。下面是一个基于计时器的简单示例：
## 资源调控代码示例 – 具有缓冲区的事件写入器

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

虽然上面的代码运行良好，但是客户端不知道其事件是否到达基础存储。为了允许进行缓冲并让客户端了解其请求所发生的情况，我们引入了以下方法以让客户端等到其事件写入 .CSV 文件：

## 资源调控代码示例 – 异步批处理

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

我们使用此类创建和维护未完成任务的列表（用于阻止客户端），在我们将缓冲的事件写入存储之后一次性完成它们。在 EventWriter 类中，我们需要执行三个操作：将 actor 类标记为可重入、返回 SubmitNext() 的结果以及刷新计时器。修改后的代码如下所示：

## 资源调控代码示例 – 具有异步批处理的缓冲

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

这看起来很容易？ 的确如此。但容易掩盖不了企业动力。借助此体系结构，我们可获得：

* 独立于位置的资源寻址。
* 只基于不断变化的参与者（代表资源）数的可调池大小。
* 客户端协调的池使用率（如图所示）或服务器端（只需想象图中的每个池前面都有一个参与者）。
* 可缩放的池添加（只需添加表示新资源的参与者）。
* 参与者（正如我们前面所演示的那样）可以按需缓存来自后端资源的结果或使用计时器预先缓存，从而减少对命中后端资源的需要。
* 高效的异步调度。
* 任何开发人员（而不仅仅是中间件专家）都熟悉的编码环境。

此模式在开发人员需要针对受约束资源进行开发或是构建大型扩大系统的方案中十分常见。


## 后续步骤
[模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)

[模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)

[模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)

[模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-thingsd)

[模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Actor 简介](/documentation/articles/service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=74-->