<properties
   pageTitle="Reliable Actors 事件"
   description="Service Fabric Reliable Actors 的事件简介。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>


# Actor 事件
Actor 事件提供一种将最佳效果通知从 Actor 发送到客户端的方法。Actor 事件旨在用于 Actor-客户端通信，且不应用于 Actor-到-Actor 的通信。

以下代码段演示如何在你的应用程序中使用 Actor 事件。

定义说明由 Actor 发布的事件的接口。此接口必须派生自 `IActorEvents` 接口。方法的参数必须为[数据协定可序列化](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)。当事件通知是单向且为最佳效果时，方法必须返回 void。

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

声明由 Actor 在 Actor 界面中发布的事件。

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    [Readonly]
    Task<string> GetGameScore();
}
```

在客户端上，实现事件处理程序。

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

在客户端上，为发布事件并订阅其事件的 Actor 创建代理。

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);
proxy.SubscribeAsync(new GameEventsHandler()).Wait();
```

如果发生故障转移，Actor 可能会故障转移到不同的过程或节点。Actor 代理管理活动的订阅，并自动重新订阅它们。你可以通过 `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API 控制重新订阅的间隔。若要取消订阅，请使用 `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API。

在 Actor 上，只需在事件发生时发布事件。如果有订阅事件的订户，Actors 运行时会向他们发送通知。

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), State.Status.Score);
```

<!---HONumber=74-->