<properties
   pageTitle="Reliable Actors 生命周期 | Azure"
   description="介绍了 Service Fabric Reliable Actors 的生命周期和垃圾回收"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="03/15/2016"
   wacn.date=""/>


# 执行组件生命周期和垃圾回收
对执行组件进行第一次调用时，将激活此执行组件。如果在一段时间内未使用执行组件，则此执行组件将停用（执行组件运行时对其进行垃圾回收）。若要配置此时间段，请参阅以下有关执行组件垃圾回收的部分。

执行组件激活时会发生什么情况？

- 当调用执行组件时，如果执行组件尚未处于活动状态，则将创建新的执行组件。
- 将加载此执行组件的状态（如果它是有状态执行组件）。
- 将调用 `OnActivateAsync` 方法（可以在执行组件实现中被覆盖）。
- 此执行组件将被添加到活动“活动执行组件”表。

执行组件停用时会发生什么情况？

- 如果在一段时间内未使用执行组件，则会从“活动执行组件”表中将其删除。
- 将调用 `OnDeactivateAsync` 方法（可以在执行组件实现中被覆盖）。这将清除此执行组件的所有计时器。

> [AZURE.TIP] Fabric 执行组件运行时发出一些[与执行组件激活和停用相关的事件](/documentation/articles/service-fabric-reliable-actors-diagnostics#actor-activation-and-deactivation-events)。它们可用于进行诊断和性能监视。

## 执行组件垃圾回收
执行组件运行时定期扫描一段时间内未使用的执行组件，并将其停用。执行组件被停用后，可使用公共语言运行时 (CLR) 对其进行垃圾回收。

出于垃圾回收的目的，怎样才能计为“正在使用”？

- 正在接收调用
- 正在调用的 `IRemindable.ReceiveReminderAsync` 方法（仅当执行组件使用提醒时适用）。

值得注意是，如果执行组件使用计时器并且调用了其计时器回调，它**不**计为“正在使用”。

在了解垃圾回收的详细信息之前，定义以下术语非常重要：

- “扫描时间间隔”。这是执行组件运行时为可以进行垃圾回收的执行组件扫描其活动执行组件表的时间间隔。此状态的默认值为 1 分钟。
- “空闲超时”。这是进行垃圾回收之前执行组件需要保持未使用（空闲）的时间。此状态的默认值为 60 分钟。

通常不需要更改这些默认值。但是，如有必要，可以在程序集级别为该程序集中的所有执行组件类型或使用 `ActorGarbageCollection` 属性在执行组件类型级别更改这些时间间隔。以下示例显示了为 HelloActor 的垃圾回收间隔进行的更改。

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

对于活动执行组件表中的每个执行组件，执行组件运行时将持续跟踪其处于空闲状态（即未使用）的时间。执行组件运行时每 `ScanIntervalInSeconds` 检查每个执行组件，以查看是否可以对它执行垃圾回收，并且如果它已空闲 `IdleTimeoutInSeconds`，则对其予以回收。

任何时候只要使用了执行组件，其空闲时间都会重置为 0。此后，仅在此执行组件再次空闲 `IdleTimeoutInSeconds`，才会对其执行垃圾回收。如果执行执行组件接口方法或执行组件提醒回调，则想到执行组件被视为已使用。如果执行其计时器回调，则执行组件**不**被视为已使用。

下图包含说明这些概念的示例。

![空闲时间示例][1]

此示例假定活动执行组件表中只有一个活动执行组件。此示例显示了执行组件方法调用、提醒和计时器对此执行组件的生存期的影响。值得一提的是有关示例的以下几点：

- ScanInterval 和 IdleTimeout 分别设置为 5 和 10。（此处的单位无关紧要，因为我们的目的只是为了说明这一概念。）
- 按照扫描间隔时间为 5 的定义，对要执行垃圾回收的执行组件进行的扫描发生在 T = 0、5、10、15、20、25。
- T = 4、8、12、16、20、24 时启动定期计时器，并执行其回调。它不会影响执行组件的空闲时间。
- 在 T = 7 时调用的执行组件方法将空闲时间重置为 0，并延迟此执行组件的垃圾回收。
- 执行组件提醒回调在 T = 14 时执行，并进一步延迟执行组件的垃圾回收。
- 在 T = 25 时的垃圾回收扫描期间，执行组件的空闲时间最终超过空闲超时时间 10，将对此执行组件进行垃圾回收。

请注意，当执行组件正在执行它的一个方法时，无论执行该方法用了多少时间，永远都不会对此执行组件进行垃圾回收。前面曾提到，执行执行组件接口方法和提醒回调可以防止通过将执行组件的空闲时间重置为 0 进行垃圾回收。执行计时器回调不会将空闲时间重置为 0。但是，执行组件的垃圾回收会推迟到计时器回调执行完毕。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=Mooncake_0418_2016-->