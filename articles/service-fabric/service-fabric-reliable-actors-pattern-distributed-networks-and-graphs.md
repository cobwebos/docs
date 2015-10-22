<properties
   pageTitle="Azure Service Fabric Actor 分布式网络和图形设计模式"
   description="有关如何使用 Service Fabric Actor 来作为分布式网络和图形对应用程序进行建模的设计模式"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/11/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：分布式网络和图形
Service Fabric Reliable Actors 天生适合对涉及关系的复杂解决方案建模和对那些作为对象的关系建模。

![][1]

如图中所说明，将用户建模为 Actor 实例（网络中的节点）非常简单。例如，用户可以通过“Friends Feed”（有时称为“follower”问题）查看他们连接到的人员的状态更新，类似于 Facebook 和 Twitter 的工作原理。
Actor 模型提供解决具体化问题的灵活性。我们可以在事件时间填充 Friends Feed，在发布更新时更新我的所有朋友的 Friends Feed，如下所示：

![][2]


## 智能缓存代码示例 – 社交网络 Friends Feed（事件时间）

填充 Friends Feed 的示例代码：

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : Actor, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

或者，我们可以对 Actor 建模，以便在查询计时器中展示并编译 Friends Feed，换而言之，当用户要求其 Friends Feed 时。我们可以使用的另一种方法是在计时器上具体化 Friends Feed，例如，每隔 5 分钟。或者，我们可以优化模型并将处理基于计时器的模型的事件时间和查询结合起来，具体取决于用户习惯，例如登录频率或发布更新的频率。在社交网络中对 Actor 建模时，还应考虑“超级用户”，即具有数以百万计追随者的用户。开发人员应对此类用户的状态和行为以不同的方式进行建模，以满足需求。同样，如果我们想要对将许多用户 Actor 连接到单个活动 Actor（中心辐射型）的活动建模，也可以执行此操作。群组聊天或游戏托管方案是两个示例。让我们来看看群组聊天示例。一组参与者创建了群组聊天 Actor，可以将消息从一个参与者分发到群组中，如以下示例所示：

## 智能缓存代码示例 – GroupChat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : Actor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

它实际进行的全部操作就是利用 Reliable Actors 的功能，即允许任何 Actor 按 id 确定群集中的任何其他 Actor，并与之进行通信，而无需担心放置、寻址、缓存、消息传送、序列化或路由。

## 后续步骤
[模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)

[模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)

[模式：有状态服务组合](/documentation/articles/service-fabric-reliable-actors-pattern-stateful-service-composition)

[模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-things)

[模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

[Service Fabric Actor 简介](/documentation/articles/service-fabric-reliable-actors-introduction)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=74-->