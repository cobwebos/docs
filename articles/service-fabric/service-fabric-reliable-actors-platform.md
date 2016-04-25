<properties
   pageTitle="Service Fabric 上的 Reliable Actors | Azure"
   description="描述 Reliable Actors 如何使用 Service Fabric 平台的功能，其中涵盖从执行组件开发人员的角度来看的一些概念。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="03/15/2016"
   wacn.date=""/>

# Reliable Actors 如何使用 Service Fabric 平台

执行组件使用 Azure Service Fabric 应用程序模型来管理应用程序生命周期。每种执行组件类型都映射到一个 Service Fabric [服务类型](/documentation/articles/service-fabric-application-model#describe-a-service)。执行组件代码会[打包](/documentation/articles/service-fabric-application-model#package-an-application)为 Service Fabric 应用程序并[部署](/documentation/articles/service-fabric-deploy-remove-applications#deploy-an-application)到群集。

## 执行组件的示例应用程序模型概念

我们以一个[使用 Visual Studio 创建](/documentation/articles/service-fabric-reliable-actors-get-started)的执行组件项目为例，来说明以上一些概念。

在 Visual Studio 中创建解决方案时，应用程序清单、服务清单和 Settings.xml 配置文件会包含在执行组件服务项目中。下面的屏幕截图中显示了这一点。

![通过 Visual Studio 创建的项目][1]

可以通过查看包含在执行组件服务项目中的应用程序清单，查找执行组件打包到其中的应用程序的类型和版本。应用程序清单中的以下代码片段是这方面的一个示例。

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

可以通过查看包含在执行组件服务项目中的服务清单，来查找执行组件类型映射到的服务类型。服务清单中的以下代码片段是这方面的一个示例。

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

使用 Visual Studio 创建应用程序包时，“生成输出”窗口中的日志会指示应用程序包的位置。例如：

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

下面是上述位置的部分列表（为简洁起见，省略了完整列表）。

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───en-us
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───en-us
                    System.Fabric.Common.Internal.Strings.resources.dll

上面的列表显示了实现 VoicemailBox 执行组件（包含在应用程序包的服务包内的代码包中）的程序集。

应用程序的后续管理（即升级和最终删除）也使用 Service Fabric 应用程序管理机制执行。有关详细信息，请参阅关于[应用程序模型](/documentation/articles/service-fabric-application-model)、[应用程序部署和删除](/documentation/articles/service-fabric-deploy-remove-applications)和[应用程序升级](/documentation/articles/service-fabric-application-upgrade)的主题。

## 执行组件服务的可伸缩性
群集管理员可以在群集中创建每种服务类型的一个或多个执行组件服务。其中每个执行组件服务可以具有一个或多个分区（类似于任何其他 Service Fabric 服务）。由于能够创建某种服务类型（可映射到执行组件类型）的多个服务以及能够为某个服务创建多个分区，因此执行组件应用程序可进行伸缩。有关详细信息，请参阅关于[可伸缩性](/documentation/articles/service-fabric-concepts-scalability)的文章。

> [AZURE.NOTE] 无状态执行组件服务要求[实例](/documentation/articles/service-fabric-availability-services#availability-of-service-fabric-stateless-services)数为 1。不支持在一个分区中具有无状态执行组件服务的多个实例。因此，无状态执行组件服务没有用于增加实例数以实现可伸缩性的选项。它们必须使用[可伸缩性文章](/documentation/articles/service-fabric-concepts-scalability)中所述的可伸缩性选项。

## 针对执行组件的 Service Fabric 分区概念
执行组件的 ID 会映射到执行组件服务的分区。执行组件在其执行组件 ID 映射到的分区中进行创建。创建执行组件时，执行组件运行时会写入 [EventSource 事件](/documentation/articles/service-fabric-reliable-actors-diagnostics#eventsource-events)，该事件指示在其中创建执行组件的分区。下面是此事件的一个示例，它指示在服务 `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`、应用程序 `fabric:/VoicemailBoxAdvancedApplication` 的分区 `b6afef61-be9a-4492-8358-8f473e5d2487` 中创建 ID 为 `-5349766044453424161` 的执行组件。

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

ID 为 `-4952641569324299627` 的另一个执行组件在相同服务的不同分区 (`5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`) 中进行创建，如下面的事件所指示。

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

> [AZURE.NOTE] 为简洁起见，省略了以上事件的某些字段。

分区 ID 可以用于获取有关分区的其他信息。例如，[Service Fabric 资源管理器](/documentation/articles/service-fabric-visualizing-your-cluster)工具可以用于查看有关分区以及它所属的服务和应用程序的信息。下面的屏幕截图显示有关分区 `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a` 的信息，该分区包含了上面示例中 ID 为 `-4952641569324299627` 的执行组件。

![有关 Service Fabric 资源管理器中的分区的信息][3]

执行组件可以通过 `Host.ActivationContext` 以及执行组件类型派生自的基类的 `Host.StatelessServiceInitialization` 或 `Host.StatefulServiceInitializationParameters` 成员，按编程方式获取分区 ID、服务名称、应用程序名称和其他特定于 Service Fabric 平台的信息。以下代码片段显示了一个示例。

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### 针对无状态执行组件的 Service Fabric 分区概念
无状态执行组件在 Service Fabric 无状态服务的分区中进行创建。执行组件 ID 用于确定在其下创建执行组件的分区。无状态执行组件服务的[实例](/documentation/articles/service-fabric-availability-services#availability-of-service-fabric-stateless-services)数必须是 1。不支持将实例数更改为任何其他值。因此，执行组件在分区中的单个服务实例内进行创建。

> [AZURE.TIP] Fabric 执行组件运行时发出一些[与无状态执行组件实例相关的事件](/documentation/articles/service-fabric-reliable-actors-diagnostics#events-related-to-stateless-actor-instances)。它们可用于进行诊断和性能监视。

创建无状态执行组件时，执行组件运行时会写入 [EventSource 事件](/documentation/articles/service-fabric-reliable-actors-diagnostics#eventsource-events)，该事件指示在其中创建执行组件的分区和实例。下面是此事件的一个示例。它表示在服务 `fabric:/HelloWorldApplication/HelloWorldActorService`、应用程序 `fabric:/HelloWorldApplication` 的分区 `8c828833-ccf1-4e21-b99d-03b14d4face3` 的实例 `130745709600495974` 中创建 ID 为 `abc` 的执行组件。

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

> [AZURE.NOTE] 为简洁起见，省略了以上事件的某些字段。

### 针对有状态执行组件的 Service Fabric 分区概念
有状态执行组件在 Service Fabric 有状态服务的分区中进行创建。执行组件 ID 用于确定在其下创建执行组件的分区。服务的每个分区可以具有一个或多个放置在群集中不同节点上的[副本](/documentation/articles/service-fabric-availability-services#availability-of-service-fabric-stateful-services)。具有多个副本可为执行组件状态提供可靠性。Azure Resource Manager 根据群集中的可用故障和升级域优化放置。相同分区的两个副本绝不会放置在相同节点上。执行组件始终在其执行组件 ID 映射到的分区的主副本中进行创建。

> [AZURE.TIP] Fabric 执行组件运行时发出一些[与有状态执行组件副本相关的事件](/documentation/articles/service-fabric-reliable-actors-diagnostics#events-related-to-stateful-actor-replicas)。它们可用于进行诊断和性能监视。

回想一下，在[前面介绍的 VoiceMailBoxActor 示例中](#service-fabric-partition-concepts-for-actors)，ID 为 `-4952641569324299627` 的执行组件是在分区 `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a` 中进行创建的。该示例中的 EventSource 事件还指示执行组件在该分区的副本 `130745418574851853` 中进行创建。这是创建执行组件时该分区的主副本。下面的 Service Fabric 资源管理器屏幕截图确认了这一点。

![Service Fabric 资源管理器中的主副本][4]

## 执行组件状态提供程序选择
执行组件运行时中包含一些默认执行组件状态提供程序。若要为执行组件服务选择相应的状态提供程序，需要了解状态提供程序如何使用基础 Service Fabric 平台功能使执行组件状态高度可用。

默认情况下，有状态执行组件使用键值存储的执行组件状态提供程序。此状态提供程序以 Service Fabric 平台提供的分布式键值存储为基础构建。此状态在托管主[副本](/documentation/articles/service-fabric-availability-services#availability-of-service-fabric-stateful-services)的节点的本地磁盘上永久保存。此状态也在托管辅助副本的节点的本地磁盘上复制并永久保存。仅当副本的仲裁已将状态提交到其本地磁盘时才能完成状态保存。键值存储具有高级功能，可检测不一致性（如错误进度）并自动更正它们。

执行组件运行时还包括 `VolatileActorStateProvider`。此状态提供程序会将状态复制到副本（状态的副本），但状态会保留在每个副本的内存中。如果一个副本停机并且再次打开，则其状态会从其他副本重新生成。但是如果所有副本同时停机，则状态数据会丢失。因此，此状态提供程序适用于其中的数据可以经受几个副本的故障以及可以经受计划故障转移（如升级）的应用程序。如果所有副本都丢失，则数据需要使用 Service Fabric 外部的机制重新创建。可以通过向执行组件类或程序集级别属性添加 `VolatileActorStateProvider` 属性，将有状态执行组件配置为使用可变执行组件状态提供程序。

下面的代码片段演示了如何将没有显式状态提供程序属性的程序集中的所有执行组件都更改为使用 `VolatileActorStateProvider`。

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

下面的代码片段演示了如何将特定执行组件类型（在此例中为 `VoicemailBox`）的状态提供程序更改为 `VolatileActorStateProvider`。

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

请注意，更改状态提供程序需要重新创建执行组件服务。不能在应用程序升级过程中更改状态提供程序。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=Mooncake_0418_2016-->