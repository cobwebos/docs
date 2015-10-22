<properties
   pageTitle="Reliable Actors 分布式计算模式"
   description="Service Fabric Reliable Actors 非常适合并行的异步消息传送、易于管理的分布式状态以及并行计算。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="09/08/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：分布式计算
我们之所以提供此模式，部分是为了观察现实生活中客户在有悖常理的少量时间内在 Service Fabric Reliable Actors 中突然进行财务计算，确切地说，就是进行风险计算的 Monte Carlo 模拟。

首先，尤其是那些不具备域特定知识的人员，与更传统的方法相反，例如 Map/Reduce 或 MPI，Azure Service Fabric 处理这种类型的工作负荷可能不太明显。

但事实证明，Azure Service Fabric 非常适合并行的异步消息传送、易于管理的分布式状态以及并行计算，如下图所示：

![][1]

在以下示例中，我们只使用 Monte Carlo 模拟计算 Pi。我们有以下 Actor：

* 负责使用 PoolTask Actors 计算 Pi 的处理器。

* 负责 Monte Carlo 模拟和向聚合器发送结果的 PoolTask。

* 负责聚合结果并将它们发送到 Finaliser 的聚合器。

* 负责计算最终结果并在屏幕上打印的 Finaliser。

## 分布式计算代码示例 – Monte Carlo 模拟

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

在 Azure Service Fabric 中聚合结果的常用方法是使用计时器。我们之所以使用无状态 Actor，出于两个主要原因：运行时会决定动态所需聚合器的数量，并因此向我们提供需要的规模；并“在本地”（换而言之，在调用 Actor 的相同接收器中）举例说明这些 Actor，从而减少网络跃点计数。下面是聚合器和 Finaliser 的外观：

## 分布式计算代码示例 – 聚合器

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : Actor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return TaskDone.Done;
    }
}
```

现在应该很清楚，我们如何可能通过适用于规模和性能的聚合器增强 Leaderboard 示例。

我们决不是断言 Azure Service Fabric 可以临时替代大数据框架的其他分布式计算或高性能计算。它构建了一些内容，可以比其他方式更好地进行处理。但是，它可以在 Azure Service Fabric 中对工作流和分布式并行计算进行建模，同时仍然获取它提供的简单性优点。

## 后续步骤
[模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)

[模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)

[模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)

[模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)

[模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-things)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

[Service Fabric Actor 简介](/documentation/articles/service-fabric-reliable-actors-introduction)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=74-->