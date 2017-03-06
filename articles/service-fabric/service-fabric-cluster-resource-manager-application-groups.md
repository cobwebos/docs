---
title: "Service Fabric 群集资源管理器 - 应用程序组 | Microsoft 文档"
description: "概述 Service Fabric 群集资源管理器中的应用程序组功能"
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6
ms.lasthandoff: 01/07/2017


---
# <a name="introduction-to-application-groups"></a>应用程序组简介
Service Fabric 的群集资源管理器通常通过将负载（通过[指标](service-fabric-cluster-resource-manager-metrics.md)表示）平均分散到整个群集来管理群集资源。 Service Fabric 还管理群集中节点的容量，以及通过[容量](service-fabric-cluster-resource-manager-cluster-description.md)的概念管理整个群集。 指标和容量非常适合用于许多工作负荷，但大量使用不同 Service Fabric 应用程序实例的模式还有其他要求。 其他要求通常包括：

* 能够在群集中为应用程序实例的服务保留容量
* 能够限制可运行应用程序中的服务的节点总数
* 定义应用程序实例本身的容量，以限制其中服务的资源总消耗量

为了满足这些要求，Service Fabric 群集资源管理器支持应用程序组。

## <a name="managing-application-capacity"></a>管理应用程序容量
应用程序容量可用于限制应用程序跨越的节点数。 它还能限制单个节点上应用程序实例的指标负载和总负载。 它还可用于在群集中为应用程序保留资源。

可以在创建新应用程序时为应用程序设置应用程序容量，还可以更新现有应用程序的应用程序容量。

### <a name="limiting-the-maximum-number-of-nodes"></a>限制最大节点数
应用程序容量的最简单用例出现在需要将应用程序实例化限制为特定的最大节点数时。 如果未指定应用程序容量，Service Fabric 群集资源管理器将根据正常规则（均衡或重组）创建和放置副本。

下图显示了已定义和未定义最大节点数的应用程序实例。 无法保证哪些服务的哪些副本或实例会放在一起，或者要使用哪些特定的节点。

<center>
![定义最大节点数的应用程序实例][Image1]
</center>

在左侧的示例中，应用程序中未设置应用程序容量，并且有三个服务。 群集资源管理器已将所有副本分散到六个可用节点，以便在群集中实现最佳均衡。 在右侧的示例中，可以看到同一个应用程序已限制到三个节点。

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
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>应用程序指标、负载和容量
应用程序组还允许用户定义与给定应用程序实例关联的指标，以及应用程序在这些指标上拥有的容量。 因此，可以跟踪、保留和限制该应用程序实例中服务的资源消耗量。

对于每个应用程序指标，可以设置两个值：

* **应用程序容量总计** – 此设置表示应用程序在特定指标上拥有的总容量。 群集资源管理器不允许在此应用程序实例中创建会导致总负载超过此值的任何新服务。 例如，假设应用程序实例的容量为 10，现有的负载为 5。 在这种情况下，不允许创建总默认负载为 10 的服务。
* **最大节点容量** – 此设置指定单个节点上应用程序中的服务副本的最大总负载。 如果节点上的总负载超过此容量，群集资源管理器会尝试将副本移到其他节点，以遵守容量限制。

## <a name="reserving-capacity"></a>保留容量
应用程序组的另一个常见用途是确保针对给定的应用程序实例保留群集中的资源。 即使该应用程序实例在该群集中没有任何服务，或者它们尚未消耗任何资源，也会发生这种容量保留。 让我们了解具体的工作原理。

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>指定节点和资源保留的最小数量
为应用程序实例保留资源需要指定两个附加参数：*MinimumNodes* 和 *NodeReservationCapacity*

* **MinimumNodes** - 我们可以指定应用程序中的服务可在其上运行的最大节点数，同样，我们也可以指定最小节点数。 此设置定义应保留资源的节点数，保证在创建应用程序实例时群集中有足够的容量。
* **NodeReservationCapacity** - 可针对应用程序中的每个指标定义 NodeReservationCapacity。 此设置定义针对任一节点（放置服务的任何副本或实例）上的应用程序保留的指标负载。

下面是容量保留的一个示例：

<center>
![定义保留容量的应用程序实例][Image2]
</center>

在左侧的示例中，应用程序中未定义任何应用程序容量。 群集资源管理器将会根据普通规则均衡所有负载。

在右侧的示例中，假设应用程序是使用以下设置创建的：

* MinimumNodes 设置为&2;
* MaximumNodes 设置为&3;
* 应用程序指标定义方式：
  * NodeReservationCapacity 为 20
  * MaximumNodeCapacity 为 50
  * TotalApplicationCapacity 为 100

Service Fabric 将在两个节点上为蓝色应用程序保留容量，不允许群集中的其他应用程序实例消耗该容量。 我们认为这些保留的应用程序容量是根据该节点上和群集中的剩余群集容量消耗和计算的。

当创建的应用程序具有保留容量时，群集资源管理器保留的容量等于 MinimumNodes * NodeReservationCapacity（针对每个指标）。 但是，仅当在特定的节点上放置了至少一个副本时，才在该节点上保留容量。 使用后面这种保留方式可以获得弹性并改善资源利用率，因为只会根据需要在节点上保留资源。

## <a name="obtaining-the-application-load-information"></a>获取应用程序负载信息
对于具有定义的应用程序容量的每个应用程序，可以获取其服务的副本报告的聚合负载的相关信息。

例如，可以使用以下 PowerShell cmdlet 检索负载：

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

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

此命令从应用程序中删除所有应用程序容量参数。 该命令会立即产生效果。 此命令完成后，群集资源管理器将重新使用默认行为来管理应用程序。 可以通过 Update-ServiceFabricApplication 再次指定应用程序容量参数。

### <a name="restrictions-on-application-capacity"></a>应用程序容量的限制
必须遵守应用程序容量参数的几项限制。 如果发生验证错误，创建或更新应用程序的操作将被拒绝并出错，且不会发生任何更改。

* 所有整数参数必须为非负数。
* MinimumNodes 不得大于 MaximumNodes。
* 如果已定义负载指标的容量，则这些容量必须遵守以下规则：
  * 节点保留容量不得大于最大节点容量。 例如，尝试在每个节点上保留三个单位时，不能将节点上的指标“CPU”的容量限制为两个单位。
  * 如果已指定 MaximumNodes，则 MaximumNodes 和最大节点容量的积不得大于应用程序容量总计。 例如，如果将负载指标“CPU”的最大节点容量设置为&8;， 将最大节点数设置为&10;。 在这种情况下，此负载指标的应用程序容量总计必须大于 80。

在（客户端）创建应用程序和在（服务器端）更新应用程序期间都会强制实施限制。

## <a name="how-not-to-use-application-capacity"></a>在哪些情况下不应使用应用程序容量
* 不要尝试使用应用程序容量功能将应用程序限制为_特定_的节点子集。 换而言之，可以指定应用程序可在最多五个节点上运行，但不应指定要在群集中具体的哪五个节点上运行。 将应用程序限制到特定的节点可以使用服务的放置约束来实现。
* 不要尝试使用应用程序容量来确保将同一应用程序中的两个服务放到一起。 若要确保服务在同一节点上运行，可以根据具体的要求，使用相关性或放置约束。

## <a name="next-steps"></a>后续步骤
* 有关可用于配置服务的其他选项的详细信息，请查看[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)中提供的其他群集资源管理器配置的相关主题
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
* 从头开始并[获取 Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)
* 有关在一般情况下指标的工作原理的详细信息，请参阅 [Service Fabric 负载指标](service-fabric-cluster-resource-manager-metrics.md)
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇[介绍 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

