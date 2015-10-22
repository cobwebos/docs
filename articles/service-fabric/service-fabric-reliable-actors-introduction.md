<properties
   pageTitle="Service Fabric Reliable Actors 概述"
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
Reliable Actors API 是 [Service Fabric](/documentation/articles/service-fabric-technical-overview) 提供的两个高级框架中的一个，另一个是 [Reliable Services API](/documentation/articles/service-fabric-reliable-services-introduction)。

基于 Actor 模式，Reliable Actors API 提供异步的单线程编程模型，该模型可简化你的代码，同时仍利用 Service Fabric 提供的可缩放性和可靠性保证。

## Actor
Actor 是封装状态和行为的独立的单线程组件。它们类似于.NET 对象，并因此提供自然的编程模型。每个 Actor 都是 Actor 类型的实例，类似于.NET 对象是 .NET 类型的实例的方式。例如，可能有用于实现计算器功能的 Actor 类型，并可能有很多在群集中各个节点分布的该类型的 Actor。每个此类 Actor 都通过 Actor ID 进行唯一标识。

## 定义和实现 Actor 接口

通过使用请求-响应模式传递异步消息，Actor 与系统的其余部分进行交互，包括其他 Actor。这些交互在接口中定义为异步方法。例如，用于实现计算器功能的 Actor 类型的接口可能定义如下：

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Actor 类型可以实现上述接口，如下所示：

```csharp
public class CalculatorActor : Actor, ICalculatorActor
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

由于方法调用及其响应最终导致群集中出现网络请求，参数和它返回的任务的结果类型必须通过平台可序列化。特别是，它们必须为[数据协定可序列化](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)。

> [AZURE.TIP]Fabric Actors 运行时发出一些[与 Actor 方法相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics#actor-method-events-and-performance-counters)。它们可用于进行诊断和性能监视。

适合 Actor 接口方法的以下规则值得一提：
- Actor 接口方法不能重载。
- Actor 接口方法不能有输出、引用或可选参数。

## Actor 通信
### Actor 代理
Actor 客户端 API 提供 Actor 实例和 Actor 客户端之间的通信。若要与 Actor 进行通信，客户端需创建实现 Actor 接口的 Actor 代理对象。客户端通过调用代理对象上的方法与 Actor 进行交互。Actor 代理可以用于客户端-到-Actor 以及 Actor-到-Actor 的通信。继续我们的计算器示例，计算器 Actor 的客户端代码可以按照如下所示方法进行编写：

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

请注意用于创建 Actor 代理对象的两条信息，即 Actor ID 和应用程序名称。Actor ID 是唯一标识 Actor 的标识符，而应用程序名称标识在其中部署 Actor 的 [Service Fabric 应用程序](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-application-model-concepts-for-actors)。

### Service Fabric 生存期

Service Fabric Actor 是虚拟的，这表示其生存期不依赖于其内存中表示形式。因此，它们不需要显式创建或销毁。Actors 运行时会在它第一次接收该 Actor 的请求时自动激活 Actor。如果 Actor 不用于特定时间，Actors 运行时则会对内存中对象执行垃圾回收，同时保持它需要稍后重新激活的 Actor 存在状态的知识。有关更多详细信息，请参阅 [Actor 生命周期和垃圾回收](/documentation/articles/service-fabric-reliable-actors-lifecycle)。

### 位置透明度和自动故障转移

若要提供高度可缩放性和可靠性，Service Fabric 在整个群集中分布 Actor，并根据需要自动将其从故障节点迁移到正常节点中。客户端上的 `ActorProxy` 类执行必要的解决方法，以便按 ID [分区](/documentation/articles/service-fabric-reliable-actors-platform#service-fabric-partition-concepts-for-actors)查找 Actor，并随之打开通信通道。如果出现通信故障和故障转移，`ActorProxy` 还会重试。这可确保即使存在故障也能可靠地传递消息，但也意味着 Actor 实现可能从相同客户端获取重复消息。

## 并发
### 基于轮次的并发

Actors 运行时为 Actor 方法提供简单的基于轮次的并发。这意味着任何时候 Actor 代码内仅有一个活动线程。

一个轮次包含完成执行 Actor 方法，以响应其他 Actor 或客户端的请求，或完成执行[计时器/提醒](/documentation/articles/service-fabric-reliable-actors-timers-reminders)回调。尽管这些方法和回调是异步的，但 Actors 运行时不会将它们交错。必须完全完成一个轮次，然后才允许启用新的轮次。换而言之，必须完整地完成当前正在执行的 Actor 方法或计时器/提醒回调，然后才允许对方法进行新的调用或回调。如果执行已从方法或回调中返回，且由方法或回调返回的任务已完成，则将方法或回调视为已完成。值得重视的是，即使在不同的方法、计时器和回调中，也遵从基于轮次的并发。

通过在轮次的开始获取按 Actor 锁并在轮次的结束释放锁，Actors 运行时强制执行基于轮次的并发。因此，基于按 Actor 基础而非所有 Actor 强制执行基于轮次的并发。Actor 方法和计时器/提醒回调可以代表不同 Actor 同时执行。

以下示例对上述概念进行了说明。请考虑实现两个异步方法（例如 *Method1* 和 *Method2*）、计时器和提醒的 Actor 类型。下图显示了代表属于此 Actor 类型的两个 Actor（*ActorId1* 和 *ActorId2*）执行这些方法和回调的时间线示例。

![][1]

上图遵循的约定是：

- 每条垂直线显示代表特定 Actor 执行方法或回调的逻辑流。
- 每条垂直线上标记的事件按时间顺序发生，发生的较新事件位于较旧事件之后。
- 时间线上使用不同的颜色对应不同的 Actor。
- 突出显示用于指示代表方法或回调保持按 Actor 锁的持续时间。

有关上述图的以下几点值得一提：

- 当代表 *ActorId2* 执行 *Method1*，以响应客户端请求 *xyz789* 时，另一个客户端请求 *abc123* 到达，也要求由 *ActorId2* 执行 *Method1*。但是，*Method1* 的后一种执行不会开始，直到完成前一种执行。同样，当正在执行 *Method1*，以响应客户端请求 *xyz789* 时，*ActorId2* 注册的提醒会启动。仅在这两个 *Method1* 执行都完成之后才执行提醒回调。所有这些都是由于正在为 *ActorId2* 强制执行基于轮次的并发。
- 同样，还为 *ActorId1* 强制执行基于轮次的并发，如代表按照串行方式发生的 *ActorId1* 执行 *Method1*、*Method2* 和计时器回调中所示。
- 代表 *ActorId1* 执行 *Method1* 与代表 *ActorId2* 对其执行重叠。这是因为仅在一个 Actor 内而非所有 Actor 中强制执行基于轮次的并发。
- 在某些方法/回调执行中，方法/回调返回的 `Task` 在方法返回之后完成。在某些其他执行中，方法/回调返回时 `Task` 已完成。在任一情况下，只有在这两种情况都发生之后才释放按 Actor 锁，即：在方法/回调返回和 `Task` 完成后。

### 重新进入

Actors 运行时默认情况下允许重新进入。这意味着如果 Actor A 的 Actor 方法调用 Actor B 上的方法，后者反过来又调用 Actor A 上的另一种方法，则允许该方法作为相同逻辑调用链上下文的一部分进行运行。所有计时器和提醒调用都使用新的逻辑上下文开始。有关更多详细信息，请参阅[重新进入](/documentation/articles/service-fabric-reliable-actors-reentrancy)部分。

### 并发保证的范围

Actors 运行时在它控制调用这些方法的情况下提供这些并发保证。例如，它针对为了响应接收客户端请求而执行的方法调用和计时器与提醒回调提供这些保证。但是，如果 Actor 代码在 Actors 运行时提供的机制之外直接调用这些方法，则运行时不能提供任何并发保证。例如，如果在与 Actor 方法返回的任务不相关联的某项任务的上下文中调用方法，或者，如果通过 Actor 自行创建的线程调用，则运行时不能提供并发保证。因此，若要执行后台操作，Actor 应使用遵从基于轮次的并发的 [Actor 计时器或 Actor 提醒](/documentation/articles/service-fabric-reliable-actors-timers-reminders)。

> [AZURE.TIP]Fabric Actors 运行时发出一些[与并发相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics#concurrency-events-and-performance-counters)。它们可用于进行诊断和性能监视。

## Actor 状态管理
利用 Fabric Actors，你能够创建无状态或有状态 Actor。

### 无状态 Actor
派生自 ``Actor`` 基类的无状态 Actor 不具有 Actors 运行时管理的任何状态。其成员变量在它们的整个内存中生存期内予以保留，就像其他任何.NET 类型一样。但是，当它们处于非活动状态一段时间后，进行垃圾回收时，其状态将丢失。同样，状态可能由于故障转移而丢失，该故障转移发生在升级期间、资源平衡操作过程中或由于 Actor 过程或其托管节点发生故障。

下面是无状态 Actor 的示例。

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### 有状态 Actor
有状态 Actor 具有在垃圾回收和故障转移过程中需要保留的状态。它们派生自 `Actor<TState>` 基类，其中 `TState` 是需要保留的状态的类型。可以通过基类上的 `State` 属性在 Actor 方法中访问状态。

下面是访问状态的有状态 Actor 的示例。

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

通过将其保留在磁盘上并在群集中的多个节点中进行复制，在垃圾回收和故障转移过程中保留 Actor 状态。这意味着，像方法参数和返回值一样，Actor 状态的类型必须为[数据协定可序列化](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)。

> [AZURE.NOTE]有关如何定义接口和 Actor State 类型的模式详细信息，请参阅[有关序列化的 Reliable Actors 说明](/documentation/articles/service-fabric-reliable-actors-notes-on-actor-type-serialization)文章。

#### Actor 状态提供程序
状态的存储和检索由 Actor 状态提供程序提供。可以通过状态提供程序特定的属性按 Actor 或为程序集内的所有 Actor 配置状态提供程序。当 Actor 激活时，其状态加载在内存中。当 Actor 方法完成时，Actors 运行时通过在状态提供程序上调用方法自动保存已修改状态。如果保存操作期间发生故障，Actors 运行时则创建新的 Actor 实例并从状态提供程序中加载最后的一致状态。

默认情况下，有状态 Actor 使用键-值存储 Actor 状态提供程序，该程序基于 Service Fabric 平台提供的分布式键-值存储构建而成。有关详细信息，请参阅有关[状态提供程序选择](/documentation/articles/service-fabric-reliable-actors-platform#actor-state-provider-choices)的主题。

> [AZURE.TIP]Actors 运行时发出一些[与 Actor 状态管理相关的事件和性能计数器](/documentation/articles/service-fabric-reliable-actors-diagnostics#actor-state-management-events-and-performance-counters)。它们可用于进行诊断和性能监视。

#### Readonly 方法
默认情况下，Actors 运行时在 Actor 方法调用、计时器回调或提醒回调完成后保存 Actor 状态。保存状态完成之前不允许执行其他 Actor 调用。

可能有不会修改状态的 Actor 方法，在这种情况下，保存状态所用的额外时间可能会影响系统的整体吞吐量。为了避免这种情况，可以将不会修改状态的方法和计时器回调标记为只读。

以下示例演示了如何使用 `Readonly` 属性将 Actor 方法标记为只读。

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

可以通过类似方式使用 `Readonly` 属性标记计时器回调。对于提醒，只读标志作为参数传递到调用以注册提醒的 `RegisterReminder` 方法。

## 后续步骤
### 概念
[Actor 生命周期和垃圾回收](/documentation/articles/service-fabric-reliable-actors-lifecycle)

[Actor 计时器和提醒](/documentation/articles/service-fabric-reliable-actors-timers-reminders)

[Actor 事件](/documentation/articles/service-fabric-reliable-actors-events)

[Actor 重新进入](/documentation/articles/service-fabric-reliable-actors-reentrancy)

[Fabric Actors 如何使用 Service Fabric 平台](/documentation/articles/service-fabric-reliable-actors-platform)

[配置 KVSActorStateProvider Actor](/documentation/articles/service-fabric-reliable-actors-KVSActorstateprovider-configuration)

[Actor 诊断和性能监视](/documentation/articles/service-fabric-reliable-actors-diagnostics)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=74-->