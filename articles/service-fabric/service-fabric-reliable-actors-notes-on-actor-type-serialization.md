<properties
   pageTitle="有关执行组件类型序列化的 Reliable Actors 说明 | Microsoft Azure"
   description="讨论了定义可序列化类的基本要求，这些类可用于定义 Service Fabric Reliable Actors 的状态和接口"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="11/13/2015"
   wacn.date=""/>

# 有关 Service Fabric Reliable Actors 类型序列化的说明

定义执行组件的接口和状态时，你应该牢记一些重要方面。类型必须是数据协定可序列化。可以在 [MSDN](https://msdn.microsoft.com/zh-cn/library/ms731923.aspx) 上找到有关数据协定的详细信息。

## 执行组件接口类型

所有方法的参数和[执行组件接口](/documentation/articles/service-fabric-reliable-actors-introduction#actors)中定义的每个方法返回的任务的结果类型必须为数据协定可序列化。这同样适用于[执行组件事件接口](/documentation/articles/service-fabric-reliable-actors-events#actor-events)中定义的方法的参数。（执行组件事件接口方法始终返回 void）。
例如，如果 `IVoiceMail` 接口将方法定义为：

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` 是已为数据协定可序列化的标准 .NET 类型。`Voicemail` 类型也必须是数据协定可序列化。

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

## 执行组件状态类

执行组件的状态必须为数据协定可序列化。例如，执行组件类定义可类似于以下内容：

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

此状态类将使用此类进行定义，其成员将分别用 **DataContract** 和 **DataMember** 属性进行标注。

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

<!---HONumber=Mooncake_0307_2016-->