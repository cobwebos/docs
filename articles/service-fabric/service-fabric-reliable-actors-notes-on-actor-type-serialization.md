<properties
   pageTitle="有关 Actor 类型序列化的 Reliable Actors 说明"
   description="讨论了定义可序列化类的基本要求，这些类可用于定义 Service Fabric Reliable Actor 状态和接口"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/11/2015"
   wacn.date=""/>

# 有关 Service Fabric Reliable Actors 类型序列化的说明

有几个需要注意的重要事项，当定义 Actor 的接口和状态时：类型需要为数据协定可序列化。可以在 [MSDN](https://msdn.microsoft.com/library/ms731923.aspx) 上找到有关数据协定的详细信息。

## Actor 接口中使用的类型

所有方法的参数和 [Actor 接口](/documentation/articles/service-fabric-reliable-actors-introduction#actors)中定义的每个方法返回的任务的结果类型需要为数据协定可序列化。这同样适用于 [Actor 事件接口](/documentation/articles/service-fabric-reliable-actors-events#actor-events)中定义的方法的参数。（Actor 事件接口方法始终返回 void）。例如，如果 `IVoiceMail` 接口将方法定义为：

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` 是已为数据协定可序列化的标准 .NET 类型。 `Voicemail` 类型必须是数据协定可序列化。

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

## Actor 状态类

Actor 状态必须为数据协定可序列化。例如，如果我们有如下所示的 Actor 类定义：

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

状态类将使用类和分别用 DataContract 和 DataMember 属性标注的其成员进行定义。

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

<!---HONumber=74-->