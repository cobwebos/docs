<properties
   pageTitle="Microsoft Azure Service Fabric Reliable Services 入门"
   description="如何创建具有无状态服务和有状态服务的 Service Fabric 应用程序。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.date="07/23/2015"
   wacn.date=""/>

# Microsoft Azure Service Fabric Reliable Services 入门

Service Fabric 应用程序包含一个或多个运行你的代码的服务。本教程将指导你完成使用 [*Reliable Services* 编程模型](/documentation/articles/service-fabric-reliable-services-introduction)创建无状态和有状态“Hello World”Service Fabric 应用程序的步骤。

无状态服务是目前大部分存在于云应用程序中的服务类型。该服务之所以被视为无状态，是因为服务本身不包含需要可靠地存储或具有高可用性的数据，换而言之，如果关闭无状态服务的实例，其所有内部状态都将丢失。在这一类服务中，必须将状态保存到外部存储（如 Azure 表或 SQL 数据库），才能实现高可用性和可靠性。

Service Fabric 引入了一种新的有状态服务：该服务可以将状态可靠地保存在其内部，并且与使用该服务的代码共存。Service Fabric 无需将状态保存到外部存储，便可实现状态的高可用性。

在本教程中，你将实现无状态服务和保留内部计数器的有状态服务。在无状态服务中，当服务重新启动或移动时，计数器的值将丢失。但是，在有状态服务中，Service Fabric 可实现计数器状态的可靠性，这样，如果在计数过程中因故中断服务执行，它可以从中断处重新开始。

## 创建无状态服务

我们先从无状态服务开始。

以**管理员**身份启动 Visual Studio 2015 RC，并新建一个名为 *HelloWorld* 的 **Service Fabric 应用程序**项目：

![使用“新建项目”对话框新建 Service Fabric 应用程序](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

然后，创建一个名为 *HelloWorldStateless* 的**无状态服务**项目：

![在第二个对话框中，创建无状态服务](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

你的解决方案现在包含 2 个项目：

 + **HelloWorld** 这是包含*服务* 的*应用程序* 项目。它还包含应用程序清单，用于描述该应用程序以及一些帮助你部署应用程序的 PowerShell 脚本。
 + **HelloWorldStateless** 这是包含无状态服务实现的服务项目。


## 实现服务

打开服务项目中的 **HelloWorld.cs** 文件。在 Service Fabric 中，服务可以运行任何一种业务逻辑。服务 API 为你的代码提供两个入口点：

 - 名为 *RunAsync* 的开放式入口点方法，可在其中开始执行任何所需的工作负荷，比如长时间运行的计算工作负荷。

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - 通信入口点，可在其中插入所选的通信堆栈（如 Web API），并在该通信堆栈中开始接收来自用户或其他服务的请求。

```C#
protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}
```

在本教程中，我们将重点介绍 `RunAsync()` 入口点方法，你可以在其中立即开始运行你的代码。项目模板包含一个递增滚动计数的 `RunAsync()` 实现示例。

> [AZURE.NOTE]有关使用通信堆栈的详细信息，请查看[带 OWIN 自托管的 Microsoft Azure Service Fabric Web API 服务入门](/documentation/articles/service-fabric-reliable-services-communication-webapi)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

当服务实例已放置并且可以执行时，平台将调用此方法。对于无状态服务，这就意味着打开服务实例。需要关闭服务实例时，将提供取消标记进行协调。在 Service Fabric 中，服务实例的此打开-关闭循环可能会由于各种原因在服务的整个生存期内出现多次，其中包括：

- 系统可能会来回移动服务实例以实现资源平衡。
- 你的代码中出现错误。
- 在应用程序或系统升级期间。
- 当基础硬件中断时。

系统将管理此业务流程，以便保持服务的高度可用和适当平衡。

`RunAsync()` 在其自己的**任务**中执行。请注意，在上面的代码段中，我们直接跳转到 **while** 循环；无需为你的工作负荷单独安排任务。取消工作负荷是一项由所提供的取消标记协调的协同操作。系统会等你的任务结束后（成功完成、取消或出现故障）再执行下一步操作。当系统请求取消时，**务必**接受取消标记，完成所有任务，然后尽快退出 `RunAsync()`。

在此无状态服务示例中，计数存储在本地变量中。不过，由于这是无状态服务，因此，所存储的值仅在其所在服务实例的当前生命周期中存在。当服务移动或重新启动时，值就会丢失。

## 创建有状态服务

若要将计数器值从无状态转换为即使在服务移动或重新启动时仍高度可用并持久存在，我们需要有状态服务。

在同一个 **HelloWorld** 应用程序中，通过右键单击该应用程序项目并选择“新建结构服务”来添加一个新的服务。

![向 Service Fabric 应用程序添加服务](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

选择“Service Fabric 有状态服务”并将其命名为“HelloWorldStateful”。单击**“添加”**。

![使用“新建项目”对话框新建 Service Fabric 有状态服务](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

你的应用程序现在应有两个服务：无状态服务 *HelloWorld* 和有状态服务 *HelloWorldStateful*。

打开 *HelloWorldStateful* 中的 **HelloWorldStateful.cs**，该文件包含以下 `RunAsync` 方法：

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

### RunAsync

有状态服务具有与无状态服务相同的入口点。主要区别在于 *Reliable Collections* 和*状态管理器* 的可用性。有状态服务中 `RunAsync()` 的操作方式与无状态服务相似，只不过在有状态服务中，平台会在执行 `RunAsync()` 前替你执行额外的任务，比如确保*状态管理器* 和 *Reliable Collections* 可供使用。

### Reliable Collections 和状态管理器

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** 是一种字典实现，允许你将状态可靠地存储在服务中。它是 Service Fabric 内置 [Reliable Collections](/documentation/articles/service-fabric-reliable-services-reliable-collections) 的一部分。利用 Service Fabric 和 Reliable Collections，现在可以将数据直接存储在服务中而无需外部持久性存储，从而使数据具备高可用性。Service Fabric 通过以下方法实现此操作：为你的服务创建并管理多个*副本*，同时提供一个 API，简化这些副本及其状态转换的管理。

Reliable Collections 可以存储任何 .NET 类型（包括自定义类型），但需要注意以下几点：

 1. Service Fabric 通过跨节点*复制* 状态并将其存储到本地磁盘，使状态具备高可用性。这意味着 Reliable Collection 中存储的所有内容都必须*可序列化*。默认情况下，Reliable Collections 使用 [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) 进行序列化，因此，在使用默认序列化程序时，务必确保你的类型[受数据协定序列化程序的支持](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx)。

 2. 当你在 Reliable Collection 上提交事务时，将复制对象以实现高可用性。Reliable Collections 中存储的对象保存在服务的本地内存中，这意味着你拥有对该对象的本地引用。

    切勿转变这些对象的本地实例而不在事务中的 Reliable Collection 上执行更新操作，因为这些更改将不会自动复制。

*StateManager* 负责为你管理 Reliable Collections。无论何时何地以名称向 StateManager 请求服务中的某个 Reliable Collection，你都能得到一个引用。不建议将对 Reliable Collection 实例的引用保存在类成员变量或属性中，因为必须特别小心，以确保在服务生命周期中，始终将引用设置为某个实例。StateManager 将为你执行此操作，并且针对重复访问进行优化。

### 事务和异步

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collections 具有许多与其 `System.Collections.Generic` 和 `System.Collections.Concurrent` 对应项相同的操作，其中包括 LINQ。不过，Reliable Collections 上的操作是异步的。这是因为系统会*复制* Reliable Collections 的写入操作，也就是说，该操作将发送到不同节点上的其他服务副本，以实现高可用性。

它们还支持*事务* 操作，因此，你可以在多个 Reliable Collections 之间保持状态的一致。例如，你可以在单个事务中，将工作项从 Reliable Queue 取消排队、对其执行操作并将结果保存在 Reliable Dictionary 中。此操作可视为原子操作，即保证整个操作都成功或都失败，因此，如果在将该工作项移出队列之后并在保存结果之前出错，将回滚整个事务，并且该工作项仍在队列中等待处理。

## 运行应用程序

返回 *HelloWorld* 应用程序。现在，你可以生成并部署你的服务。按“F5”，即可生成应用程序并部署到本地群集。

如果服务正在运行，你可以在“诊断事件”窗口中看到生成的 ETW 事件。请注意，应用程序中会同时显示无状态服务和有状态服务的事件。你可以通过单击“暂停”按钮暂停数据流，然后通过展开消息来查看消息详细信息。

>[AZURE.NOTE]在运行应用程序之前，请确保正在运行本地开发群集。请查看[入门指南](/documentation/articles/service-fabric-get-started)以获取本地环境设置。

![在 Visual Studio 中查看诊断事件](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## 后续步骤

[在 Visual Studio 中调试 Service Fabric 应用程序](/documentation/articles/service-fabric-debugging-your-application)

[带 OWIN 自托管的 Microsoft Azure Service Fabric Web API 服务入门](/documentation/articles/service-fabric-reliable-services-communication-webapi)

[深入了解 Reliable Collections](/documentation/articles/service-fabric-reliable-services-reliable-collections)

[管理 Service Fabric 服务](/documentation/articles/service-fabric-manage-your-service-index)

[Reliable Services 的开发人员参考](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=74-->