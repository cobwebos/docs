---
title: "有关执行组件类型序列化的 Reliable Actors 说明 | Microsoft 文档"
description: "讨论了定义可序列化类的基本要求，这些类可用于定义 Service Fabric Reliable Actors 的状态和接口"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 4b48b893e5a3bf5620f00a336576efe1ad63def8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>有关 Service Fabric Reliable Actors 类型序列化的说明
所有方法的参数、执行组件接口中每个方法返回的任务的结果类型和执行组件的状态管理器中存储的对象都必须是[数据协定可序列化](https://msdn.microsoft.com/library/ms731923.aspx)。 这同样适用于[执行组件事件接口](service-fabric-reliable-actors-events.md)中定义的方法的参数。 （执行组件事件接口方法始终返回 void。）

## <a name="custom-data-types"></a>自定义数据类型
在此示例中，以下执行组件接口定义了一个返回自定义数据类型（名为 `VoicemailBox`）的方法：

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

此接口由使用状态管理器来存储 `VoicemailBox` 对象的执行组件实现：

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

在此示例中，如果 `VoicemailBox` 对象出现以下情况，将序列化该对象：

* 在执行组件实例和调用方之间传输该对象。
* 该对象保存在状态管理器中，即持久保存在磁盘中，并且已复制到其他节点。

Reliable Actor 框架使用 DataContract 序列化。 因此，自定义数据对象及其成员必须分别使用 **DataContract** 和 **DataMember** 属性进行批注。

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>后续步骤
* [执行组件生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
* [执行组件计时器和提醒](service-fabric-reliable-actors-timers-reminders.md)
* [执行组件事件](service-fabric-reliable-actors-events.md)
* [执行组件可重入性](service-fabric-reliable-actors-reentrancy.md)
* [执行组件多态性和面向对象的设计模式](service-fabric-reliable-actors-polymorphism.md)
* [执行组件诊断和性能监视](service-fabric-reliable-actors-diagnostics.md)
