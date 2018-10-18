---
title: 在 Azure Service Fabric 执行组件中实现功能 | Microsoft Docs
description: 介绍如何以继承 StatefulService 时所使用的方式编写自己的可实现服务级功能的执行组件服务。
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
ms.openlocfilehash: 89161f3dad68c4b208f4badc548e2057c7ed58c1
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022043"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>在执行组件服务中实现服务级功能

如[服务分层](service-fabric-reliable-actors-platform.md#service-layering)中所述，执行组件服务本身是一项可靠的服务。 可以编写自己的派生自 `ActorService` 的服务。 还可以实现服务级功能，所用方式与继承有状态服务的方式相同，例如：

- 服务备份和还原。
- 共享给所有执行组件的功能，例如断路器。
- 对执行组件服务自身和每个执行组件的远程过程调用。

## <a name="use-the-actor-service"></a>使用执行组件服务

执行组件实例可访问运行这些实例的执行组件服务。 通过执行组件服务，执行组件实例可以编程方式获取服务上下文。 服务上下文包括分区 ID、服务名称、应用程序名称以及其他特定于 Azure Service Fabric 平台的信息。

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

与所有 Reliable Services 一样，执行组件服务必须使用 Service Fabric 运行时中的服务类型注册。 为了使执行组件服务能够运行执行组件实例，还必须将执行组件类型注册到执行组件服务。 `ActorRuntime` 注册方法将为执行组件执行此操作。 最简单的情况是，只需注册执行组件类型，然后执行组件服务就会使用默认的设置。

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

或者，可以使用此注册方法提供的 lambda 自己构造执行组件服务。 然后可以配置执行组件服务和显式构造执行组件实例。 可以通过执行组件的构造函数向执行组件注入依赖项。

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

执行组件服务实现了 `IActorService` (C#) 或 `ActorService` (Java)，后者又实现了 `IService` (C#) 或 `Service` (Java)。 此接口由 Reliable Services 远程控制使用，该远程控制允许对服务方法执行远程过程调用。 它包含可以通过服务远程控制进行远程调用的服务级别方法。 可以使用它来[枚举](service-fabric-reliable-actors-enumerate.md)和[删除](service-fabric-reliable-actors-delete-actors.md)执行组件。


## <a name="custom-actor-service"></a>自定义执行组件服务

通过使用执行组件注册 lambda，可以注册从 `ActorService` (C#) 和 `FabricActorService` (Java) 派生的自定义执行组件服务。 然后，可以通过编写从 `ActorService` (C#) 或 `FabricActorService` (Java) 继承的服务类来实现自己的服务级功能。 自定义执行组件服务从 `ActorService` (C#) 或 `FabricActorService` (Java) 继承了所有执行组件运行时功能， 可用来实现你自己的服务方法。

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

## <a name="implement-actor-backup-and-restore"></a>实现执行组件备份和还原

自定义执行组件服务可通过利用于 `ActorService` 中已存在的远程侦听器公开备份执行组件数据的方法。 有关示例，请参阅[备份和还原执行组件](service-fabric-reliable-actors-backup-and-restore.md)。

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>使用远程处理 V2（与接口兼容）堆栈的执行组件

远程处理 V2（与接口兼容，称为 V2_1）堆栈具有 V2 远程处理堆栈的所有功能。 其接口与远程处理 V1 堆栈兼容，但不与 V2 和 V1 后向兼容。 若要在不影响服务可用性的情况下从 V1 升级到 V2_1，请执行下一部分的步骤。

需要进行以下更改才能使用远程处理 V2_1 堆栈：

 1. 在执行组件接口中添加以下程序集属性。
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. 生成并升级执行组件服务和执行组件客户端项目即可开始使用 V2 堆栈。

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>在不影响服务可用性的情况下将执行组件服务升级到远程处理 V2（与接口兼容）堆栈

此更改是一个两步升级过程。 请按顺序执行以下步骤。

1. 在执行组件接口中添加以下程序集属性。 此属性针对执行组件服务启动两个侦听器：V1（现有）和 V2_1 侦听器。 通过此项更改升级执行组件服务。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. 在完成上一升级后升级执行组件客户端。
此步骤确保执行组件代理使用远程处理 V2_1 堆栈。

3. 此步骤是可选的。 可以通过更改上述属性来删除 V1 侦听器。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>使用远程处理 V2 堆栈的执行组件

借助 2.8 版 NuGet 包，用户现在可以使用性能更好且提供自定义序列化等功能的远程处理 V2 堆栈。 远程处理 V2 不后向兼容现有的远程处理堆栈（现在称为 V1 远程处理堆栈）。

需要进行以下更改才能使用远程处理 V2 堆栈。

 1. 在执行组件接口中添加以下程序集属性。

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. 生成并升级执行组件服务和执行组件客户端项目即可开始使用 V2 堆栈。

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>在不影响服务可用性的情况下将执行组件服务升级到远程处理 V2 堆栈

此更改是一个两步升级过程。 请按顺序执行以下步骤。

1. 在执行组件接口中添加以下程序集属性。 此属性针对执行组件服务启动两个侦听器：V1（现有）和 V2 侦听器。 通过此项更改升级执行组件服务。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. 在完成上一升级后升级执行组件客户端。
此步骤确保执行组件代理使用远程处理 V2 堆栈。

3. 此步骤是可选的。 可以通过更改上述属性来删除 V1 侦听器。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
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
