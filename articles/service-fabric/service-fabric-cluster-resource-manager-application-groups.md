---
title: "Service Fabric 群集Resource Manager - 应用程序组 | Microsoft 文档"
description: "概述 Service Fabric 群集 Resource Manager 中的应用程序组功能"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 3212631ede7125bd849c2d9ba86ba2a0747d69ca
ms.contentlocale: zh-cn
ms.lasthandoff: 08/19/2017

---
# <a name="introduction-to-application-groups"></a>应用程序组简介
Service Fabric 的群集 Resource Manager 通常通过将负载（通过[指标](service-fabric-cluster-resource-manager-metrics.md)表示）平均分散到整个群集来管理群集资源。 Service Fabric 管理群集中节点的容量，并通过[容量](service-fabric-cluster-resource-manager-cluster-description.md)管理整个群集。 指标和容量非常适合用于许多工作负荷，但大量使用不同 Service Fabric 应用程序实例的模式还有其他要求。 例如，你可以采取以下建议：

- 为某些命名应用程序实例内的服务保留群集节点上的某些容量
- 限制在其上运行命名应用程序实例内的服务的节点总数，而不是分散到整个群集上
- 定义命名应用程序实例本身的容量，限制其中的服务数量或服务的资源总消耗量

为了满足这些要求，Service Fabric 群集资源管理器支持名为应用程序组的功能。

## <a name="limiting-the-maximum-number-of-nodes"></a>限制最大节点数
应用程序容量的最简单用例出现在需要将应用程序实例限制为特定的最大节点数时。 这样会将该应用程序实例中的所有服务整合到一定数量的计算机上。 尝试预测该命名应用程序实例内的服务所使用的物理资源或设置其上限时，整合会非常有用。 

下图显示已定义和未定义最大节点数的应用程序实例：

<center>
![定义最大节点数的应用程序实例][Image1]
</center>

在左侧的示例中，应用程序未定义最大节点数，并且有三个服务。 群集资源管理器已将所有副本分散到六个可用节点，从而在群集中实现最佳均衡（默认行为）。 在右侧的示例中，可以看到同一个应用程序已限制到三个节点。

控制此行为的参数称为 MaximumNodes。 可在创建应用程序期间设置此参数，或针对已在运行的应用程序实例更新此参数。

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

在节点集中，群集资源管理器并不能保证会将哪些服务对象放在一起或者会使用哪些节点。

## <a name="application-metrics-load-and-capacity"></a>应用程序指标、负载和容量
利用应用程序组，还可以定义与给定命名应用程序实例关联的指标，以及该应用程序实例在这些指标上拥有的容量。 利用应用程序指标，可以跟踪、保留和限制该应用程序实例中服务的资源消耗量。

对于每个应用程序指标，可以设置两个值：

- **应用程序容量总计** – 此设置表示应用程序在特定指标上拥有的总容量。 群集 Resource Manager 不允许在此应用程序实例中创建会导致总负载超过此值的任何新服务。 例如，假设应用程序实例的容量为 10，现有的负载为 5。 不允许创建总默认负载为 10 的服务。
- **最大节点容量** – 此设置指定单个节点上应用程序的最大总负载。 如果负载超过此容量，则群集资源管理器会将副本移到其他节点，降低负载。


Powershell：

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#：

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>保留容量
应用程序组的另一个常见用途是确保针对给定的应用程序实例保留群集中的资源。 创建应用程序实例时，会始终保留空间。

即使出现以下情况，也会在群集中立即为应用程序保留空间：
- 应用程序实例已创建，但其中尚不具有任何服务
- 应用程序实例中的服务数每次都会发生更改 
- 服务存在，但不会消耗资源 

为应用程序实例保留资源需要指定两个附加参数：MinimumNodes 和 NodeReservationCapacity

- MinimumNodes - 定义应用程序实例应在其上运行的最小节点数。  
- NodeReservationCapacity - 此设置是应用程序的各项指标。 应用程序中的服务在节点上运行，该值是为该节点上的该应用程序保留的该指标的量。

结合使用 MinimumNodes 和 NodeReservationCapacity 可以保证为群集中的应用程序保留最小负载。 如果群集中存在的剩余容量比所需要的总保留容量小，则无法创建应用程序。 

下面是容量保留的一个示例：

<center>
![定义保留容量的应用程序实例][Image2]
</center>

在左侧的示例中，应用程序中未定义任何应用程序容量。 群集 Resource Manager 会根据普通规则均衡所有负载。

在右侧的示例中，假设 Application1 是使用以下设置创建的：

- MinimumNodes 设置为 2
- 应用程序指标定义方式：
  - NodeReservationCapacity 为 20

Powershell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric 为 Application1 保留两个节点上的容量，并不允许 Application2 的服务使用该容量，即使此时 Application1 中的服务唯有消耗负载。 我们认为这些保留的应用程序容量是根据该节点上和群集中的剩余容量消耗和计算的。  可以立即从剩余的群集容量中扣除保留容量，但是仅当特定节点上至少放置一个服务对象时，才从该节点的容量扣除保留的消耗量。 使用后面这种保留方式可以获得弹性并改善资源利用率，因为只会根据需要在节点上保留资源。

## <a name="obtaining-the-application-load-information"></a>获取应用程序负载信息
对于每个应用程序，如果具有为一个或多个指标定义的应用程序容量，可以获取其服务的副本报告的聚合负载的相关信息。

Powershell：

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

ApplicationLoad 查询返回针对应用程序指定的应用程序容量的基本信息。 这些信息包括“最小节点数”和“最大节点数”信息，以及应用程序当前占用的节点数。 此外，还包括有关每个应用程序负载指标的信息，例如：

* 指标名称：指标的名称。
* 保留容量：在群集中为此应用程序保留的群集容量。
* 应用程序负载：此应用程序的子副本的总负载。
* 应用程序容量：允许的最大应用程序负载值。

## <a name="removing-application-capacity"></a>删除应用程序容量
为应用程序设置应用程序容量参数后，可以使用更新应用程序 API 或 PowerShell cmdlet 来删除这些参数。 例如：

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

此命令从应用程序实例中删除所有应用程序容量管理参数。 其中包括 MinimumNodes、MaximumNodes 和应用程序的指标（如果有）。 该命令会立即产生效果。 此命令完成后，群集资源管理器会使用默认行为管理应用程序。 可以通过 `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` 再次指定应用程序容量参数。

### <a name="restrictions-on-application-capacity"></a>应用程序容量的限制
必须遵守应用程序容量参数的几项限制。 如果存在验证错误，则不会发生任何更改。

- 所有整数参数必须为非负数。
- MinimumNodes 不得大于 MaximumNodes。
- 如果已定义负载指标的容量，则这些容量必须遵守以下规则：
  - 节点保留容量不得大于最大节点容量。 例如，尝试在每个节点上保留三个单位时，不能将节点上的指标“CPU”的容量限制为两个单位。
  - 如果已指定 MaximumNodes，则 MaximumNodes 和最大节点容量的积不得大于应用程序容量总计。 例如，如果将负载指标“CPU”的最大节点容量设置为 8， 将最大节点数设置为 10。 在这种情况下，此负载指标的应用程序容量总计必须大于 80。

在创建和更新应用程序的过程中，都会强制限制。

## <a name="how-not-to-use-application-capacity"></a>在哪些情况下不应使用应用程序容量
- 不要尝试使用应用程序容量功能将应用程序限制为_特定_的节点子集。 换而言之，可以指定应用程序可在最多五个节点上运行，但不应指定要在群集中具体的哪五个节点上运行。 将应用程序限制到特定的节点可以使用服务的放置约束来实现。
- 不要尝试通过使用应用程序容量确保将同一应用程序中的两个服务放在同一节点上。 改用[地缘](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)或[放置约束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。

## <a name="next-steps"></a>后续步骤
- 有关配置服务的详细信息，请参阅[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)
- 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
- 从头开始并[获取 Service Fabric 群集 Resource Manager 简介](service-fabric-cluster-resource-manager-introduction.md)
- 有关在一般情况下指标的工作原理的详细信息，请参阅 [Service Fabric 负载指标](service-fabric-cluster-resource-manager-metrics.md)
- 群集 Resource Manager 提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇[介绍 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

