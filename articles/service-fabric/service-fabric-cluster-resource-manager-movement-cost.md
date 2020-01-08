---
title: Service Fabric 群集资源管理器：移动成本
description: 了解 Service Fabric 服务的移动成本，以及如何将其指定为适合任何结构需求（包括动态配置）。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563337"
---
# <a name="service-movement-cost"></a>服务移动成本
尝试确定要对群集进行哪些更改时，Service Fabric 群集资源管理器考虑的一个因素是这些更改的成本。 “成本”这一概念根据能够改进的群集量而权衡。 移动服务以满足均衡、碎片整理和其他要求时，成本是一项考虑因素。 目标是以最稳妥或最便宜的方式满足这些要求。

移动服务可将 CPU 时间和网络带宽的成本降到最低。 对于有状态服务，需要复制消耗额外内存和磁盘的这些服务的状态。 将 Azure Service Fabric 群集资源管理器所提出的解决方案成本降到最低有助于避免使用不必要的群集资源。 但是，也不希望忽略可大幅改善群集中资源分配的解决方案。

群集资源管理器提供两种方式来计算和限制成本，同时尝试管理群集。 第一种机制只需计算它所进行的每次移动。 如果生成的两个解决方案的均衡值（分数）大致相同，则群集资源管理器倾向于采用成本（移动总量）最低的解决方案。

此策略适用。 但是，对于默认负载或静态负载，在任何复杂的系统中不太可能所有移动都一样。 有些移动的成本可能要昂贵得多。

## <a name="setting-move-costs"></a>设置移动成本 
可在服务创建时为其指定默认的移动成本：

PowerShell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#： 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

此外，还可在服务创建后为该服务动态指定或更新 MoveCost： 

PowerShell： 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#：

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>动态指定每个副本的移动成本

前面的代码片段全部用于从服务本身外部同时指定整个服务的 MoveCost。 但是，当特定服务对象的移动成本在其生命周期内发生变化时，移动成本最为有用。 由于服务本身可能最了解移动给定时间的成本是多少，因此服务可以在运行时使用 API 来报告自己的移动成本。 

C#：

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>移动成本的影响
MoveCost 有五个级别：零、低、中、高和 VeryHigh。 下列规则适用：

* Movecost 彼此相关，但零和 VeryHigh 除外。 
* 零移动成本表示移动不会产生成本，不应计入解决方案的分数。
* 将移动成本设置为 "高" 或 "VeryHigh *" 不能*保证副本*永远*不会移动。
* 仅当群集中存在无法以任何其他方式修复的约束冲突时，才会移动具有 VeryHigh 移动成本的副本（即使它需要移动多个其他副本来修复冲突）



<center>

在选择要移动的副本][Image1]
时，![移动成本作为一个因素 </center>

MoveCost 可帮助我们在达成对等的均衡时，查找整体导致最少中断且最容易实现的解决方案。 服务的成本概念可以相对于许多事项。 计算移动成本时的最常见因素包括：

- 服务必须移动的状态或数据量。
- 客户端断开连接的成本。 移动主要副本的成本通常比移动次要副本的成本更高。
- 中断某些进行中操作的成本。 某些数据存储级别的操作，或者为了响应客户端调用而执行的操作，成本都很高。 在特定的时间点后，除非有必要，否则我们不会停止这些操作。 因此，执行该操作时，将增加此服务对象的移动成本，以减小其移动的可能性。 当操作完成之后，可以将成本设置恢复正常。

> [!IMPORTANT]
> 应认真考虑使用 VeryHigh 移动成本，因为这大大限制了分类资源管理器在群集中查找全局最佳放置解决方案的能力。 仅当群集中存在无法以任何其他方式修复的约束冲突时，才会移动具有 VeryHigh 移动成本的副本（即使它需要移动多个其他副本来修复冲突）

## <a name="enabling-move-cost-in-your-cluster"></a>在群集中启用移动成本
为了考虑到更为精细的 MoveCost，必须在群集中启用 MoveCost。 如不进行此设置，则会使用计数移动的默认模式来计算 MoveCost，并忽略 MoveCost 报告。


ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤
- Service Fabric 群集资源管理器使用指标管理群集中的消耗和容量。 有关指标以及如何配置指标的详细信息，请查看[在 Service Fabric 中使用指标管理资源消耗和负载](service-fabric-cluster-resource-manager-metrics.md)。
- 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看[均衡 Service Fabric 群集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
