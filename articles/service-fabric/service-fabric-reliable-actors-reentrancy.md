<properties
   pageTitle="Reliable Actors 可重入性"
   description="Service Fabric Reliable Actors 的可重入性简介"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>


# Reliable Actor 可重入性
默认情况下，Fabric Actor 允许逻辑调用基于上下文的可重入性。这使参与者在处于相同调用上下文链中时可重入。例如，如果参与者 A 将消息发送给参与者 B，而后者将消息发送到参与者 C。则作为消息处理的一部分，如果参与者 C 调用参与者 A，则消息可重入，这是允许的。属于不同调用上下文的任何其他消息会在参与者 A 上受阻，直到它完成处理。

要禁止逻辑调用基于上下文的可重入性的参与者可以通过使用 `ReentrantAttribute(ReentrancyMode.Disallowed)` 修饰 actor 类来禁用它。

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

下面的代码演示了将重入模式设置为 `ReentrancyMode.Disallowed` 的 actor 类 。在这种情况下，如果参与者向另一个参与者发送可重入消息，则会引发类型为 `FabricException` 的异常。

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=74-->