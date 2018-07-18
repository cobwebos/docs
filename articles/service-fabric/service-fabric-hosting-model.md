---
title: Azure Service Fabric 托管模型 | Microsoft Docs
description: 介绍已部署的 Service Fabric 服务和服务主机进程的副本（或实例）之间的关系。
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d56bb10041e3baffddf6fd4121a6e1f7ba8e0632
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206314"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric 托管模型
本文概述 Azure Service Fabric 提供的应用程序托管模型，并介绍**共享进程**模型和**独占进程**模型之间的差异。 本文介绍已部署的应用程序在 Service Fabric 节点上的外观，以及服务和服务主机进程的副本（或实例）之间的关系。

在继续阅读之前，请确保了解[在 Service Fabric 中为应用程序建模][a1]中所述的各种概念及其相互关系。 

> [!NOTE]
> 在本文中，除非明确说明，否则：
>
> - “副本”指有状态服务的副本或无状态服务的实例。
> - *CodePackage* 被视为与注册 *ServiceType* 的 *ServiceHost* 进程相同，并且 CodePackage 托管该 *ServiceType* 的服务副本。
>

让我们通过示例来了解托管模型。 假设有一个 *ApplicationType* 的“MyAppType”，它的 *ServiceType* 为“MyServiceType”。 “MyServiceType”由 *ServicePackage*“MyServicePackage”提供，此 ServicePackage 的 *CodePackage* 为“MyCodePackage”。 “MyCodePackage”在运行时注册 *ServiceType*“MyServiceType”。

假设有三个节点群集，创建“MyAppType”类型的应用程序 **fabric:/App1**。 在此应用程序 **fabric:/App1** 中创建“MyServiceType”类型的服务 **fabric:/App1/ServiceA**。 该服务有 2 个分区（例如 **P1** 和 **P2**），每个分区有 3 个副本。 下图显示了将此应用程序部署在节点后的视图。


![已部署应用程序的节点视图的示意图][node-view-one]


Service Fabric 已激活启动了“MyServicePackage”的“MyCodePackage”，“MyServicePackage”正在托管两个分区中的副本。 群集中的所有节点将都具有相同的视图，因为每个分区中选择的副本数与群集中的节点数相等。 在应用程序 **fabric:/App1** 中创建另一个服务 **fabric:/App1/ServiceB**。 该服务有 1 个分区（例如 **P3**），每个分区有 3 个副本。 下图显示节点上的新视图：


![已部署应用程序的节点视图的示意图][node-view-two]


Service Fabric 将服务 **fabric:/App1/ServiceB** 的 **P3** 分区的新副本放置在“MyServicePackage”的现有激活中。 现在， 创建另一个“MyAppType”类型的应用程序 **fabric:/App2**。 在 **fabric:/App2** 内部创建服务 **fabric:/App2/ServiceA**。 该服务有 2 个分区（**P4** 和 **P5**），每个分区有 3 个副本。 下图显示了新的节点视图：


![已部署应用程序的节点视图的示意图][node-view-three]


Service Fabric 激活了“MyServicePackage”的新副本，此“MyServicePackage”启动“MyCodePackage”的新副本。 服务 **fabric:/App2/ServiceA** 两个分区（**P4** 和 **P5**）中的副本均放置在“MyCodePackage”的此新副本中。

## <a name="shared-process-model"></a>共享进程模型
上一部分描述了 Service Fabric 提供的默认托管模型，也称为共享进程模型。 在此模型中，对于给定应用程序，节点（启动其自身包含的所有 *CodePackage*）上只会激活给定 *ServicePackage* 的一个副本。 给定 *ServiceType* 的所有服务的所有副本均放置在注册该 *ServiceType* 的 *CodePackage* 中。 换言之，给定 ServiceType 的节点上的所有服务的所有副本共享相同的进程。

## <a name="exclusive-process-model"></a>独占进程模型
Service Fabric 提供的另一个托管模型是独占进程模型。 在此模型中的给定节点上，每个副本驻留在其自身的专用进程中。 Service Fabric 激活 *ServicePackage*（启动其自身包含的所有 *CodePackage*）的一个新副本。 副本放置在注册 *ServiceType*（副本所属服务的类型）的 *CodePackage* 中。 

如果使用 Service Fabric 5.6 或更高版本，可以在创建服务时选择独占进程模型（使用 [PowerShell][p1]、[REST][r1] 或 [FabricClient][c1]）。 将 **ServicePackageActivationMode** 指定为“ExclusiveProcess”。

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

如果应用程序清单中有一个默认服务，则可以通过指定 **ServicePackageActivationMode** 属性选择独占进程模型：

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
现在，在应用程序 **fabric:/App1** 中创建另一个服务 **fabric:/App1/ServiceC**。 该服务有 2 个分区（例如 **P6** 和 **P7**），每个分区有 3 个副本。 将 **ServicePackageActivationMode** 设置为“ExclusiveProcess”。 下图显示节点上的新视图：


![已部署应用程序的节点视图的示意图][node-view-four]


如此处所示，Service Fabric 激活了“MyServicePackage”的两个新副本（分别对应分区 **P6** 和 **P7** 中的每个副本）。 Service Fabric 已将每个副本放置在其 *CodePackage* 的专用副本中。 对给定应用程序使用独占进程模型时，给定 *ServicePackage* 可以有多个副本在节点上保持活动状态。 在前面的示例中，有 3 个“MyServicePackage”副本对于 **fabric:/App1** 保持活动状态。 “MyServicePackage”的这 3 个活跃副本均具有与自身关联的 **ServicePackageActivationId**。 此 ID 在应用程序 **fabric:/App1** 中标识该副本。

如果仅对应用程序使用共享进程模型，则节点上只有一个活动的 *ServicePackage* 副本。 此 *ServicePackage* 激活的 **ServicePackageActivationId** 是空字符串。 例如，**fabric:/App2** 就存在这种情况。

> [!NOTE]
>- 共享进程托管模型与 **ServicePackageActivationMode**（相当于 **SharedProcess**）对应。 这是默认的托管模型，并且创建服务时无需指定 **ServicePackageActivationMode**。
>
>- 独占进程托管模型与 **ServicePackageActivationMode**（相当于 **ExclusiveProcess**）对应。 若要使用此设置，应在创建服务时显式指定。 
>
>- 若要查看服务的托管模型，请查询[服务说明][p2]，并查看 **ServicePackageActivationMode** 的值。
>
>

## <a name="work-with-a-deployed-service-package"></a>使用已部署服务包
节点上 *ServicePackage* 的活动副本称为[已部署服务包][p3]。 使用独占进程模型创建服务时，对于给定应用程序，同一 *ServicePackage* 可能有多个已部署服务包。 执行特定于已部署服务包的操作时，应提供 **ServicePackageActivationId** 来标识特定的已部署服务包。 例如，在[报告已部署服务包的运行状况][p4]或[重启已部署服务包的代码包][p5]时，请提供 ID。

通过在节点上查询[已部署服务包][p3]的列表，可以找到已部署服务包的 **ServicePackageActivationId**。 在节点上查询[已部署服务包][p6]、[已部署副本][p7]和[已部署代码包][p8]时，查询结果还包含父级已部署服务包的 ServicePackageActivationId。

> [!NOTE]
>- 在共享进程托管模型下，对于指定应用程序，指定节点上只会激活一个 *ServicePackage* 副本。 ServicePackage 的 **ServicePackageActivationId** 是空字符串，不需要在执行已部署服务包相关操作时指定。 
>
> - 在独占进程托管模型下，对于指定应用程序，指定节点上可能有一个或多个活动的 *ServicePackage* 副本。 每个活动副本具有非空的 **ServicePackageActivationId**，需要在执行已部署服务包相关操作时指定。 
>
> - 如果省略了 **ServicePackageActivationId**，则它默认为空字符串。 如果存在共享进程模型下激活的已部署服务包，则需在此包上执行操作。 否则，操作会失败。
>
> - 不要对 **ServicePackageActivationId** 执行一次查询或缓存。 此 ID 是动态生成的，会出于各种原因发生更改。 执行需要 **ServicePackageActivationId** 的操作前，应先在节点上查询[已部署服务包][p3]的列表。 然后使用查询结果中的 **ServicePackageActivationId** 执行原始操作。
>
>

## <a name="guest-executable-and-container-applications"></a>来宾可执行文件和容器应用程序
Service Fabric 将[来宾可执行文件][a2]和[容器][a3]应用程序视为自包含式无状态服务。 *ServiceHost*（进程或容器）中没有 Service Fabric 运行时。 由于这些是自包含服务，因此每个 *ServiceHost* 包含的副本数不适用于这些服务。 这些服务使用的最常见配置是单分区，其中 [InstanceCount][c2]等于 -1（每个群集节点上运行一个服务代码副本）。 

这些服务的默认 **ServicePackageActivationMode** 是 **SharedProcess**，在这种情况下，对于指定应用程序，Service Fabric 只会在节点上激活一个 *ServicePackage* 副本。  这意味着只有一个服务代码副本将运行节点。 如果希望有多个服务代码副本在节点上运行，请在创建服务时将 **ServicePackageActivationMode** 指定为 **ExclusiveProcess**。 例如，在创建 *ServiceType*（在 *ServiceManifest* 中指定）的多个服务多个服务（*Service1* 到 *ServiceN*）或服务具有多个分区时，可以执行此操作。 

## <a name="change-the-hosting-model-of-an-existing-service"></a>更改现有服务的托管模型
目前无法将现有服务的托管模型从共享进程更改为独占进程（或反之）。

## <a name="choose-between-the-hosting-models"></a>在托管模型之间进行选择
应该评估哪种托管模型最符合自己的要求。 共享进程模型更充分地利用操作系统资源，因为产生的进程较少，同一进程上的多个副本可以共享端口等。 但是，如果一个副本遇到了需要关闭服务主机的错误时，会影响同一进程中的所有其他副本。

 独占进程模型将每个副本合理地分离在其自己的进程中。 某个副本出错不会影响到其他副本。 对于通信协议不支持端口共享的情况，此模型会很有用。 它支持在副本级别应用资源管理。 但是，独占进程消耗的操作系统资源较多，因为它为节点上的每个副本生成一个进程。

## <a name="exclusive-process-model-and-application-model-considerations"></a>独占进程模型和应用程序模型注意事项
对于大多数应用程序，在 Service Fabric 中为应用程序建模时，请对每个 *ServicePackage* 使用同一种 *ServiceType*。 

在某些情况下，Service Fabric 还允许每个 *ServicePackage* 包含多种 *ServiceType* （以及一个 *CodePackage* 可以注册多种 *ServiceType*）。 以下是这些配置适用的一些方案：

- 希望通过生成较少进程和使用副本密度较高的进程来优化资源利用率。
- 不同 *ServiceType* 的副本需要共享一些初始化程度高或内存成本高的常见数据。
- 有免费的服务产品，并且想要通过将所有服务副本放置在同一进程来限制资源使用率。

独占进程托管模型不适用于每个 *ServicePackage* 具有多个 *ServiceType* 的应用程序模型。 这是因为每个 *ServicePackage* 具有多个 *ServiceType* 的目的是提高副本间的资源共享以及每个进程的副本密度。 独占进程模型旨在实现不同的效果。

请考虑这种情况：每个 *ServicePackage* 有多个 *ServiceType*，并且不同的 *CodePackage* 分别注册一个 *ServiceType*. 假设有一个具有 2 个 CodePackage 的 ServicePackage“MultiTypeServicePackge”：

- “MyCodePackageA”注册 ServiceType“MyServiceTypeA”。
- “MyCodePackageB”注册 ServiceType“MyServiceTypeB”。

现在，我们创建应用程序 **fabric:/SpecialApp**。 在 **fabric:/SpecialApp** 中，使用独占进程模型创建以下 2 个服务：

- “MyServiceTypeA”类型的服务 **fabric:/SpecialApp/ServiceA** 有 2 个分区（例如 **P1** 和 **P2**），每个分区有 3 个副本。
- “MyServiceTypeB”类型的服务 **fabric:/SpecialApp/ServiceB** 有 2 个分区（**P3** 和 **P4**），每个分区有 3 个副本。

在给定节点上，两个服务将分别有两个副本。 由于使用独占进程模型创建了服务，Service Fabric 将为每个复本激活一个新“MyServicePackage”副本。 每个活动的“MultiTypeServicePackge”将启动“MyCodePackageA”和“MyCodePackageB”的一个副本。 但是，“MyCodePackageA”或“MyCodePackageB”中只有一个将托管副本（为此副本激活了“MultiTypeServicePackge”）。 下图显示了节点视图：


![已部署应用程序的节点视图的示意图][node-view-five]


为服务 **fabric:/SpecialApp/ServiceA** 的 **P1** 分区的副本激活“MultiTypeServicePackge”时，“MyCodePackageA”将托管该副本。 “MyCodePackageB”正在运行。 类似地，为服务 **fabric:/SpecialApp/ServiceB** 的 **P3** 分区的副本激活“MultiTypeServicePackge”时，“MyCodePackageB”将托管该副本。 “MyCodePackageA”正在运行。 因此，每个 *ServicePackage* 的 *CodePackage*（注册不同的 *ServiceType*）数量越多，冗余资源使用率就越高。 
 
 但是，如果使用共享进程模型创建服务 **fabric:/SpecialApp/ServiceA** 和 **fabric:/SpecialApp/ServiceB**，Service Fabric 将只为应用程序 **fabric:/SpecialApp** 激活一个“MultiTypeServicePackge”副本。 “MyCodePackageA”托管服务 **fabric:/SpecialApp/ServiceA** 的所有副本。 “MyCodePackageB”托管服务 **fabric:/SpecialApp/ServiceB** 的所有副本。 下图显示了此设置中的节点视图： 


![已部署应用程序的节点视图的示意图][node-view-six]


在上面的示例中，你可能会想，如果“MyCodePackageA”同时注册“MyServiceTypeA”和“MyServiceTypeB”并且没有“MyCodePackageB”，那么将不会运行冗余的 *CodePackage*。 这是对的，但此应用程序模型不适用于独占进程托管模型。 如果目的是将每个副本放入其自己的专用进程，则不需要从同一个 *CodePackage* 注册两个 *ServiceType*。 只需将每个 *ServiceType* 放入其自身的 *ServicePackage* 即可。

## <a name="next-steps"></a>后续步骤
[打包应用程序][a4]并准备好进行部署。

[部署和删除应用程序][a5]。 此文介绍如何使用 PowerShell 来管理应用程序实例。

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
