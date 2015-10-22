<properties
   pageTitle="Reliable Actors 生命周期"
   description="介绍了 Service Fabric Reliable Actors 的生命周期和垃圾回收"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>


# Actor 生命周期和垃圾回收
对其进行首次调用时，Actor 将激活，如果在某个时间段内未使用，则会停用（Actors 运行时执行垃圾回收）。若要配置此时间段，请参阅以下有关 Actor 垃圾回收的部分。

Actor 激活时会发生什么情况？

- 当有 Actor 的调用，且它尚未处于活动状态时，则创建新的 Actor。
- （如果它是有状态 Actor）加载其状态
- 调用 `OnActivateAsync` 方法（可以在 Actor 实现中被覆盖）。
- 将它添加到活动 Actor 表。

Actor 停用时会发生什么情况？

- 如果在某个时间段内未使用 Actor，则会从活动 Actor 表中将其删除。
- 调用 `OnDeactivateAsync` 方法（可以在 Actor 实现中被覆盖），这样就会清除 Actor 的所有计时器。

> [AZURE.TIP]Fabric Actors 运行时发出一些[与 Actor 激活和停用相关的事件](/documentation/articles/service-fabric-reliable-actors-diagnostics#actor-activation-and-deactivation-events)。它们可用于进行诊断和性能监视。

## Actor 垃圾回收
Actors 运行时定期扫描一段时间内尚未使用的 Actor，并将其停用。将它们停用之后，可以由 CLR 对其执行垃圾回收。

出于垃圾回收的目的，怎样才能计为“正在使用”？

- 接收调用。
- 正在调用的 `IRemindable.ReceiveReminderAsync` 方法（仅当 Actor 使用提醒时适用）。

值得注意是，如果 Actor 使用计时器并且调用了其计时器回调，它**不**计为“正在使用”。

进入垃圾回收的详细信息之前，定义以下术语非常重要：

- *扫描间隔* - 这是 Actors 运行时为可以进行垃圾回收的 Actor 扫描其活动 Actor 表的间隔。此状态的默认值为 1 分钟。
- *空闲超时* - 这是进行垃圾回收之前 Actor 需要保持未使用（空闲）的时间。此状态的默认值为 60 分钟。

通常不需要更改这些默认值。但是，如有必要，可以在程序集级别为该程序集中的所有 Actor 类型或使用 `ActorGarbageCollection` 属性在 Actor 类型级别更改这些间隔。以下示例显示了为 HelloActor 的垃圾回收间隔进行的更改。

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

若要在程序集级别更改 `ActorGarbageCollection` 属性的默认值，请将以下代码段添加到 `AssemblyInfo.cs`。

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

对于其活动 Actor 表中的每个 Actor，Actors 运行时保持跟踪它处于空闲状态（即未使用）的时间。Actors 运行时每 `ScanIntervalInSeconds` 检查每个 Actor，以查看是否可以对它执行垃圾回收，并且如果它已空闲 `IdleTimeoutInSeconds`，则对其予以收集。

任何时候使用 Actor 时，其空闲时间都会重置为 0。此后，如果再次空闲 `IdleTimeoutInSeconds`，只能对 Actor 执行垃圾回收。如果执行 Actor 接口方法或 Actor 提醒回调，则想到 Actor 被视为已使用。如果执行其计时器回调，则 Actor **不**被视为已使用。

下图包含说明这些概念的示例。

![][1]

示例假定活动 Actor 表中只有一个活动 Actor，并显示 Actor 方法调用、提醒和计时器对此 Actor 的生存期的影响。值得一提的是有关示例的以下几点：

- 在示例中，ScanInterval 和 IdleTimeout 分别设置为 5 和 10（单位在这里无关紧要，因为我们的目的仅仅是为了说明概念）。
- 对执行垃圾回收的 Actor 进行的扫描发生在 T = 0、5、10、15、20、25，由 ScanInterval (=5) 定义。
- T = 4、8、12、16、20、24 时启动定期计时器，并执行其回调。它不会影响 Actor 的空闲时间。
- 在 T = 7 时调用 Actor 方法，将空闲时间重置为 0，并延迟 Actor 的垃圾回收。
- Actor 提醒回调在 T = 14 时执行，并进一步延迟 Actor 的垃圾回收。
- 在垃圾回收期间，在 T = 25 时进行扫描，Actor 的空闲时间最终超过 IdleTimeout (=10)，并且对 Actor 进行垃圾回收。

请注意，当它正在执行某一方法时，无论执行该方法用了多少时间，永远都不会对 Actor 进行垃圾回收。前面曾提到，执行 Actor 接口方法和提醒回调可以防止通过将 Actor 的空闲时间重置为 0 进行垃圾回收。执行计时器回调不会将空闲时间重置为 0。但是，Actor 的垃圾回收会推迟到计时器回调执行完毕。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=74-->