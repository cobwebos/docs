<properties
   pageTitle="Service Fabric Reliable Actors 概述 | Microsoft Azure"
   description="Service Fabric Reliable Actors 编程模型简介"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# Service Fabric Reliable Actors 简介
Reliable Actors API 是 [Azure Service Fabric](/documentation/articles/service-fabric-technical-overview) 提供的两个高级框架中的一个，另一个是 [Reliable Services API](/documentation/articles/service-fabric-reliable-services-introduction)。

根据执行组件模式，Reliable Actors API 提供异步的单线程编程模型，该模型可简化你的代码，同时仍利用 Service Fabric 提供的可扩展性和可靠性。

## 执行组件
执行组件是封装状态和行为的独立的单线程组件。它们类似于.NET 对象，因此它们可以提供自然的编程模型。每个执行组件都是执行组件类型的实例，类似于.NET 对象是 .NET 类型的实例的方式。例如，一个执行组件类型可以实现一个计算器的功能，此类型的许多执行组件可以在群集中的各个节点上分布。每个此类执行组件都通过 Actor ID 进行唯一标识。

## 定义并实现执行组件接口

通过使用请求-响应模式传递异步消息，执行组件与系统的其余部分进行交互，包括其他执行组件。这些交互在接口中定义为异步方法。例如，用于实现计算器功能的执行组件类型的接口可能定义如下：

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

一个执行组件类型可以实现上述接口，如下所示：

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

由于方法的调用及其响应最终导致跨群集的网络请求，因此这些调用所返回的参数和任务的结果类型必须可通过平台进行序列化。特别是，它们必须为[数据协定可序列化](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)。

> [AZURE.TIP] Service Fabric 执行组件运行时发出一些[与执行组件方法相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics#actor-method-events-and-performance-counters)。它们可用于进行诊断和性能监视。

以下与执行组件接口方法相关的规则值得一提：
- 执行组件接口方法不能重载。
- 执行组件接口方法不能有输出、引用或可选参数。

## 执行组件通信
### 执行组件代理
Reliable Actors 客户端 API 提供一个执行组件实例和一个执行组件客户端之间的通信。若要与执行组件进行通信，客户端需创建实现执行组件接口的执行组件代理对象。客户端通过调用代理对象上的方法与执行组件进行交互。执行组件代理可以用于从客户端到执行组件以及从执行组件到执行组件的通信。继续我们的计算器示例，计算器执行组件的客户端代码可以编写为：

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

请注意用于创建执行组件代理对象的两条信息为执行组件 ID 和应用程序名称。执行组件 ID 可以唯一标识此执行组件，而应用程序名称标识在其中部署执行组件的 [Service Fabric 应用程序](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-application-model-concepts-for-actors)。

### 执行组件生存期

Service Fabric Actor 是虚拟的，这表示其生存期不依赖于其内存中表示形式。因此，它们不需要显式创建或销毁。执行组件运行时会在它第一次接收该执行组件的请求时自动激活执行组件。如果一段时间未使用某个执行组件，则执行组件运行时将回收此内存对象。它还将掌握此执行组件的存在信息，以便将来重新激活。有关详细信息，请参阅[执行组件生命周期和垃圾回收](/documentation/articles/service-fabric-reliable-actors-lifecycle)。

### 位置透明度和自动故障转移

若要提供高度可缩放性和可靠性，Service Fabric 在整个群集中分布执行组件，并根据需要自动将其从故障节点迁移到正常节点中。客户端上的 `ActorProxy` 类执行必要的解决方法，以便按 ID [分区](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-actors)查找执行组件，并随之打开通信通道。如果出现通信故障和故障转移，`ActorProxy` 还会重新查找执行组件。这可以确保尽管存在故障，也能可靠传递消息。但这也意味着执行组件的实现可能会收到来自同一客户端的重复消息。

## 并发
### 基于轮次的访问

执行组件运行时提供简单的基于轮次的模型用于访问执行组件方法。这意味着任何时候执行组件代码内仅有一个活动线程。

一个轮次包含用于响应其他执行组件或客户端的请求的执行组件方法的完全执行，或者是[计时器/提醒](/documentation/articles/service-fabric-reliable-actors-timers-reminders)回调的完全执行。即使这些方法和回调是异步的，执行组件运行时也不会交替执行它们。必须彻底完成一个轮次，才允许启动新的轮次。换而言之，在允许新的方法调用或回调之前必须彻底完成当前正在执行的执行组件方法或计时器/提醒回调。如果执行已从方法或回调中返回，且由方法或回调返回的任务已完成，则将此方法或回调视为已完成。值得强调的是，即使在不同的方法、计时器和回调中，也遵从基于轮次的并发执行。

通过在轮次的开始获取按执行组件锁并在轮次的结束释放锁，执行组件运行时强制执行基于轮次的并发。因此，基于按执行组件基础而非所有执行组件强制执行基于轮次的并发。执行组件方法和计时器/提醒回调可以代表不同执行组件同时执行。

以下示例对上述概念进行了说明。请考虑实现两个异步方法（即 *Method1* 和 *Method2*）、一个计时器和一个提醒的执行组件类型。下图显示了代表属于此执行组件类型的两个执行组件（*ActorId1* 和 *ActorId2*）执行这些方法和回调的时间线示例。

![Reliable Actors 运行时基于轮次的并发执行和访问][1]

上图遵循以下约定：

- 每条垂直线显示代表特定执行组件执行方法或回调的逻辑流。
- 每条垂直线上标记的事件按时间顺序发生，较新事件发生在较旧事件之后。
- 时间线上使用不同的颜色对应不同的执行组件。
- 突出显示用于指示代表方法或回调保持按执行组件锁的持续时间。

有关上述图的以下几点值得一提：

- 当代表 *ActorId2* 执行 *Method1*，以响应客户端请求 *xyz789* 时，另一个客户端请求 (*abc123*) 到达，也要求由 *ActorId2* 执行 *Method1*。但是，在前面的执行完成之前，不会开始此 *Method1* 的第二次执行。同样，当正在执行 *Method1*，以响应客户端请求 *xyz789* 时，*ActorId2* 注册的提醒会启动。仅在这两个 *Method1* 执行都完成之后才执行提醒回调。所有这些都是由于正在为 *ActorId2* 强制执行基于轮次的并发。
- 同样，也会针对 *ActorId1* 强制执行基于轮次的并发，如代表 *ActorId1* 以串行方式执行 *Method1* 、 *Method2* 和计时器回调所演示的。
- 代表 *ActorId1* 执行 *Method1* 与代表 *ActorId2* 对其执行重叠。这是因为仅在同一个执行组件内，而不是在不同执行组件之间强制执行基于轮次的并发执行。
- 在某些方法/回调执行中，此方法/回调返回的 `Task` 在方法返回之后完成。在其他一些执行中，在方法/回调返回之前 `Task` 已完成。在这两种情况下，仅在方法/回调已返回，且 `Task` 已完成后才会释放按执行组件的锁。

### 重新进入

执行组件运行时默认情况下允许重新进入。这意味着如果 *执行组件 A* 的执行组件方法调用 *执行组件 B* 上的方法，后者反过来又调用 *执行组件 A* 上的另一种方法，则允许该方法运行。这是因为它是同一逻辑调用链上下文的一部分。所有计时器和提醒调用都使用新的逻辑上下文开始。有关详细信息，请参阅 [Reliable Actors 可重入性](/documentation/articles/service-fabric-reliable-actors-reentrancy)。

### 并发保证的范围

执行组件运行时在它控制调用这些方法的情况下提供这些并发保证。例如，它为响应客户端请求而执行的方法调用和计时器与提醒回调提供这些保证。但是，如果执行组件代码在执行组件运行时提供的机制之外直接调用这些方法，则运行时不能提供任何并发保证。例如，如果在与执行组件方法返回的任务不相关联的某项任务的上下文中调用方法，则运行时不能提供并发性保证。如果通过执行组件依据其自身创建的线程调用方法，那么运行时也不能提供并发性保证。因此，若要执行后台操作，执行组件应使用遵从基于轮次的并发执行的[执行组件计时器和执行组件提醒](/documentation/articles/service-fabric-reliable-actors-timers-reminders)。

> [AZURE.TIP] Service Fabric 执行组件运行时发出一些[与并发性相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics#concurrency-events-and-performance-counters)。它们可用于进行诊断和性能监视。

## 执行组件状态管理
你可以使用 Service Fabric 创建无状态或有状态执行组件。

### 无状态执行组件
派生自 `StatelessActor` 基类的无状态执行组件不具有执行组件运行时管理的任何状态。其成员变量在它们的整个内存生存期内予以保留，就像其他任何 .NET 类型一样。但是，当它们处于非活动状态一段时间后，进行垃圾回收时，其状态将丢失。同样，状态可能由于故障转移而丢失，该故障转移可发生在升级期间、资源平衡操作过程中或由于执行组件进程或其托管节点发生故障而引发。

下面是无状态执行组件的示例：

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### 有状态执行组件
有状态执行组件具有在垃圾回收和故障转移过程中需要保留的状态。它们派生自 `StatefulActor<TState>`，其中 `TState` 是需要保留的状态的类型。可以通过基类上的 `State` 属性在执行组件方法中访问此状态。

下面是访问此状态的有状态执行组件的示例：

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

通过将执行组件状态保留在磁盘上并在群集的多个节点中进行复制，可在垃圾回收和故障转移过程中保留执行组件状态。这意味着，像方法参数和返回值一样，此执行组件状态的类型必须为[数据协定可序列化](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)。

> [AZURE.NOTE] 有关如何定义接口和执行组件状态类型的详细信息，请参阅[有关序列化的 Reliable Actors 说明](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)文章。

####执行组件状态提供程序
状态的存储和检索由执行组件状态提供程序提供。可以通过状态提供程序特定的属性按执行组件或为程序集内的所有执行组件配置状态提供程序。当执行组件被激活时，其状态在内存中加载。当执行组件方法完成时，执行组件运行时通过在状态提供程序上调用方法自动保存已修改的状态。如果**保存**操作期间发生故障，执行组件运行时将创建新的执行组件实例并从状态提供程序中加载最后一个一致状态。

默认情况下，有状态执行组件使用键-值存储执行组件状态提供程序，该程序基于 Service Fabric 平台提供的分布式键-值存储构建而成。有关详细信息，请参阅有关[状态提供程序选择](/documentation/articles/service-fabric-reliable-actors-platform#actor-state-provider-choices)的主题。

> [AZURE.TIP] 执行组件运行时发出一些[与执行组件状态管理相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics#actor-state-management-events-and-performance-counters)。它们在诊断和性能监视中很有用。

#### 只读方法
默认情况下，执行组件运行时在执行组件方法调用、计时器回调或提醒回调完成后保存执行组件状态。保存状态完成之前不允许执行其他执行组件调用。

尽管如此，可能存在一些未修改状态的执行组件方法。在这种情况下，保存状态所用的额外时间可能会影响系统的整体吞吐量。为了避免这种情况，可以将未修改状态的方法和计时器回调标记为只读。

以下示例显示了如何使用 `Readonly` 属性将执行组件方法标记为只读。

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

可以通过类似方式使用 `Readonly` 属性标记计时器回调。对于提醒，只读标志作为参数传递到调用以注册提醒的 `RegisterReminder` 方法。

## 后续步骤
[执行组件生命周期和垃圾回收](/documentation/articles/service-fabric-reliable-actors-lifecycle)

[执行组件计时器和提醒](/documentation/articles/service-fabric-reliable-actors-timers-reminders)

[执行组件事件](/documentation/articles/service-fabric-reliable-actors-events)

[执行组件可重入性](/documentation/articles/service-fabric-reliable-actors-reentrancy)

[Reliable Actors 如何使用 Service Fabric 平台](/documentation/articles/service-fabric-reliable-actors-platform)

[配置 KVSActorStateProvider 执行组件](/documentation/articles/service-fabric-reliable-actors-kvsactorstateprovider-configuration)

[执行组件诊断和性能监视](/documentation/articles/service-fabric-reliable-actors-diagnostics)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=Mooncake_0307_2016-->