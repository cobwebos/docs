---
title: 在 Azure Service Fabric 执行组件中实现功能 | Microsoft Docs
description: 介绍如何通过与继承 StatefulService 相同的方式来编写可实现服务级功能的执行组件服务。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212885"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>在执行组件服务中实现服务级功能
如[服务分层](service-fabric-reliable-actors-platform.md#service-layering)中所述，执行组件服务本身是一项可靠的服务。  可以编写自己的派生自 `ActorService` 的服务，并且以与继承 StatefulService 时一样的方式实现服务级别的功能，例如：

- 服务备份和还原。
- 共享给所有执行组件的功能，例如断路器。
- 对执行组件服务自身和每个执行组件的远程过程调用。

## <a name="using-the-actor-service"></a>使用执行组件服务
执行组件实例可访问运行这些实例的执行组件服务。 通过执行组件服务，执行组件实例可以编程方式获取服务上下文。 服务上下文包括分区 ID、服务名称、应用程序名称以及其他特定于 Service Fabric 平台的信息：

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

与所有 Reliable Services 一样，执行组件服务必须使用 Service Fabric 运行时中的服务类型注册。 为了使执行组件服务能够运行执行组件实例，还必须向执行组件服务注册执行组件类型。 `ActorRuntime` 注册方法将为执行组件执行此操作。 最简单的情况是，只需注册执行组件类型，然后隐式使用具有默认设置的执行组件服务：

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

或者，可以使用此注册方法提供的 lambda 自己构造执行组件服务。 可以配置执行组件服务和显式构造执行组件实例，从中可通过执行组件的构造函数向执行组件注入依赖项：

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>执行组件服务方法
执行组件服务实现了 `IActorService` (C#) 或 `ActorService` (Java)，后者又实现了 `IService` (C#) 或 `Service` (Java)。 这是 Reliable Services 远程控制所使用的接口，该远程控制允许对服务方法执行远程过程调用。 它包含可以通过服务远程控制进行远程调用的服务级别方法，并允许[枚举](service-fabric-reliable-actors-enumerate.md)和[删除](service-fabric-reliable-actors-delete-actors.md)执行组件。


## <a name="custom-actor-service"></a>自定义执行组件服务
通过使用执行组件注册 lambda，可以注册从 `ActorService` (C#) 和 `FabricActorService` (Java) 派生的自定义执行组件服务。 在此自定义执行组件服务中，可以通过编写从 `ActorService` (C#) 或 `FabricActorService` (Java) 继承的服务类来实现自己的服务级功能。 自定义执行组件服务从 `ActorService` (C#) 或 `FabricActorService` (Java) 继承了所有执行组件运行时功能，可用来实现自己的服务方法。

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>实现执行组件备份和还原
自定义执行组件服务可通过利用于 `ActorService` 中已存在的远程侦听器公开备份执行组件数据的方法。  有关示例，请参阅[备份和还原执行组件](service-fabric-reliable-actors-backup-and-restore.md)。

## <a name="actor-using-remoting-v2-stack"></a>使用 Remoting V2 堆栈的参与者
借助 2.8 Nuget 包，用户现在可以使用更高性能的 Remoting V2 堆栈，它提供自定义序列化等功能。 Remoting V2 不与现有的 Remoting 堆栈（称为 V1 Remoting 堆栈）向后兼容。

需要进行以下更改才能使用 Remoting V2 堆栈。
 1. 在参与者界面中添加以下程序集属性。
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. 生成并升级 ActorService 和参与者客户端项目以开始使用 V2 堆栈。

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>在不影响服务可用性的情况下将参与者服务升级到 Remoting V2 堆栈。
这项更改是包括 2 个步骤的升级过程。 按照所列的顺序执行步骤。

1.  在参与者界面中添加以下程序集属性。 此属性针对 ActorService 启动两个侦听器：V1（现有）和 V2 侦听器。 通过此项更改升级 ActorService。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. 完成上述升级后，升级 ActorClients。
此步骤确保参与者代理使用 Remoting V2 堆栈。

3. 此步骤是可选的。 更改上述属性可删除 V1 侦听器。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>后续步骤
* [执行组件状态管理](service-fabric-reliable-actors-state-management.md)
* [执行组件生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
* [执行组件 API 参考文档](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 代码示例](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
