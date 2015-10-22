<properties
   pageTitle="Reliable Actors 智能缓存设计模式"
   description="有关如何在基于 Web 的应用程序上使用 Reliable Actors 作为缓存基础结构的设计模式"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：智能缓存
Web 层、缓存层和存储层（偶尔还有辅助角色层）的组合几乎是当今应用程序的标准组成部分。缓存层通常对性能至关重要，实际上本身可能由多个层组成。许多缓存都是简单键值对，而用作缓存的其他系统（如 [Redis](http://redis.io)）提供了更丰富的语义。尽管如此，任何特殊的缓存层在语义方面都会受到限制，更重要的是，它还要作为另一层来管理。相反，如果对象只是将状态保留在本地变量中，并且这些对象可以自动拍摄快照或保存到持久存储中，那又会如何呢？ 此外，丰富的集合（如列表、排序集、队列和就此而言的任何其他自定义类型）只需作为成员变量和方法来建模。

![][1]

## 排行榜示例
以排行榜为例 — 一个 Leaderboard 对象需要维护玩家及其分数的排序列表，以便我们可以查询它。例如对于“前 100 个玩家”，或是要查找某个玩家在排行榜中相对于其上和其下 +- N 个玩家的位置。使用传统工具的典型解决方案需要对 Leaderboard 对象（支持插入一个名为 Score 的新元组<Player  Points>的集合）进行“GET”操作，对它进行排序，最后将它“PUT”回缓存。我们可能会对 Leaderboard 对象执行 LOCK（GETLOCK、PUTLOCK）操作以实现一致性。让我们来使用一个基于参与者的解决方案，其中状态和行为是联系在一起的。有两个选项：

* 将 Leaderboard 集合作为参与者的一部分来实现，
* 或使用参与者作为我们可以保留在成员变量中的集合的接口。首先我们来了解一下接口可能的表现形式：

## 智能缓存代码示例 – Leaderboard 接口

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

接下来，我们实现此接口，使用后一种选项并在参与者中封装此集合的行为：

## 智能缓存代码示例 – Leaderboard 参与者

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

类的状态成员提供参与者的状态，在上面的示例代码中，它还提供用于读/写数据的方法。

## 智能缓存代码示例 – Leaderboard 集合

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

没有数据传送，没有锁，只是在分布式运行时中操作远程对象，为多个客户端提供服务，如同它们是仅为一个客户端提供服务的单个应用程序中的单一对象。下面是示例客户端：

## 智能缓存代码示例 – 调用 Leaderboard 参与者

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

输出如下所示：

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## 缩放体系结构
你可能会觉得上面的示例会在 Leaderboard 实例中形成瓶颈。例如，如果我们计划支持成百上千的玩家，情况会如何？ 应对这种情况的一种方法可能是引入如同缓冲区一样操作的无状态聚合器 — 保存部分分数（例如小计），然后定期将它们发送给 Leaderboard 参与者，这可以维护最终 Leaderboard。我们将在稍后更详细地讨论此“聚合”技术。此外，我们不需要考虑互斥、信号或是传统上让并发程序正确运行所需的其他并发构造。下面是另一个缓存示例，它演示了可以使用参与者实现的丰富语义。这次我们在 Actor 实现中实现优先级队列的逻辑（数字越低，优先级越高）。IJobQueue 的接口类似于以下内容：

## 智能缓存代码示例 – Job 队列接口

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

我们还需要定义 Job 项：

## 智能缓存代码示例 – Job

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

最后，我们精细地实现 IJobQueue 接口。请注意，为清楚起见，我们在此处省略了优先级队列的实现详细信息。可以在随附的示例中找到示例实现。

## 智能缓存代码示例 – Job 队列

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

输出如下所示：

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## 参与者可提供灵活性
在上面的示例 Leaderboard 和 JobQueue 中，我们使用了两种不同的技术：

* 在 Leaderboard 示例中，我们将 Leaderboard 对象作为参与者中的私有成员变量进行封装，仅仅提供此对象的接口（同时针对其状态和功能）。

* 另一方面，在 JobQueue 示例中，我们将参与者本身作为优先级队列进行实现，而不是引用在其他位置定义的另一个对象。

参与者使开发人员可以灵活地将丰富的对象结构定义为参与者的一部分，或引用参与者外部的对象图。在缓存方面，参与者可以进行后写或直写，或者我们可以在成员变量粒度上使用不同的技术。换而言之，我们可以完全控制保留的内容以及保留的时间。我们不必保留瞬时状态，或是我们可以从已保存状态生成的状态。那么该如何填充这些参与者缓存？ 可通过多种方法来实现此目的。参与者提供了名为 OnActivateAsync() 和 OnDectivateAsync() 的虚方法，以便告知我们何时激活和停用参与者的实例。请注意，参与者在向它发送第一个请求时按需激活。我们可以如同在直读中一样使用 OnActivateAsync() 来按需填充状态（可能是从外部的稳定存储进行）。或者我们可以根据定时器填充状态，例如一个提供基于最新货币汇率的转换函数的 Exchange Rate 参与者。此参与者可以定期（例如每 5 秒）从外部服务填充其状态，并将状态用于转换函数。请参阅以下示例：

## 智能缓存代码示例 – 汇率转换器

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

智能缓存实质上可提供：

* 通过来自内存的服务请求实现的高吞吐量/低延迟。
* 永久性存储上请求到项目的单实例路由和单线程序列化（无争用）。
* 语义操作，例如，Enqueue(Job item)。
* 易于实现的后写或直写。
* LRU（最近最少使用）项目（资源管理）的自动收回。
* 自动弹性和可靠性。


## 后续步骤
[模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)

[模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)

[模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)

[模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-things)

[模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

[Service Fabric Actor 简介](/documentation/articles/service-fabric-reliable-actors-introduction)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=74-->