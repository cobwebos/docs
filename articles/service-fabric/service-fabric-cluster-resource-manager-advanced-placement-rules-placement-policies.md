---
title: "Service Fabric 群集资源管理器 - 放置策略 | Microsoft 文档"
description: "概述 Service Fabric 服务的其他放置策略和规则"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Service Fabric 服务的放置策略
有许多不同的其他规则可能需要在某些少数情况下配置。 这些情况的一些示例包括：
* 如果 Service Fabric 群集跨越跨某段地理距离（如多个本地数据中心）或跨 Azure 区域
* 如果你的环境跨多个地缘政治控制区域或一些其他情况：你有要关注的法律或政策边界
* 由于群集中的通信历经很长距离或经过某些速度较慢或不太可靠的网络，因此有实际性能/延迟注意事项。

在此类情况下，给定服务可能有必要始终在某些区域中运行或永远不在某些区域中运行。 同样，可能有必要尝试将主副本放在某些区域中以最大程度地减少最终用户延迟。

高级放置策略包括：

1. 无效域
2. 所需域
3. 首选域
4. 不允许副本打包

以下大多数控件都能通过节点属性和放置约束来配置，但有一些控件比较复杂。 为了使操作更简单，Service Fabric 群集资源管理器提供了这些附加放置策略。 与其他放置约束一样，可以基于每个命名服务实例来配置放置策略并进行动态更新。

## <a name="specifying-invalid-domains"></a>指定无效域
InvalidDomain 放置策略可让你指定特定的容错域对此工作负荷是无效的。 此策略可确保特定的服务绝对不会在特定的区域中运行（例如，出于地缘政治或公司政策的原因）。 可以通过单独的策略指定多个无效域。

<center>
![无效域示例][Image1]
</center>

代码：

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>指定所需域
所需域放置策略要求服务的所有有状态副本或无状态服务实例存在于指定的域中。 可以通过独立的策略指定多个所需域。

<center>
![所需域示例][Image2]
</center>

代码：

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>指定主副本的首选域
首选主域是一个有趣的控件，因为它允许选择容错域，如果能够这样做，主副本就必须放置在该域中。 如果一切运行正常，主副本最终将在此域中。 如果该域或主副本发生故障，或出于某种原因而关闭，则主副本将迁移到其他某个位置。 如果这个新位置不在首选域中，群集资源管理器将尽可能快地将它移回到首选域。 当然，此设置仅适用于有状态服务。 对于跨越 Azure 区域或多个数据中心但希望主副本放置在某个位置的群集，此策略最有用。 使主副本离其用户近有助于减少延迟，尤其是对于读取操作，更为明显。

<center>
![首选主域和故障转移][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>要求在所有域之间分布副本，并且不允许打包
副本_通常_分布到群集正常运行的域上，但有些情况下，给定分区的副本可能最终暂时打包到单个域。 例如，假设群集有九个节点在三个容错域（fd:/0、fd:/1 和 fd:/2）中，而你的服务有三个副本。 假设 fd:/1 和 fd:/2 中用于这些副本的节点发生故障。 此时，群集资源管理器通常会首选这些相同容错域中的其他节点。 在此例中，假设由于容量问题这些域中的其他节点都无效。 如果群集资源管理器生成这些副本的替换位置，它只能选择 fd:/0 中的节点。 但是，执行_该操作_就造成了违反容错域约束的情况。 同时，这也会增加整个副本集发生故障或丢失的可能性（如果 FD 0 会永久丢失的话）。 有关约束和约束优先级的其他一般信息，请参阅[此主题](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)。

如果你曾经看到过类似 `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain` 的运行状况警告，则可能已遇到这种情况或类似情况。 这种情况很少见，但它可能发生，而且通常这些情况是暂时性的，因为这些节点会恢复正常。 如果这些节点确实一直处于关闭状态，并且群集资源管理器需要生成替换位置，通常最适合的容错域中有其他可用节点。

某些工作负荷宁可始终具有目标副本数，即使将它们打包到更少的域，也是如此。 这些工作负荷押注不会全部同时出现永久性域故障，并且通常可以恢复本地状态。 其他工作负荷则宁可早停机也不愿冒数据不正确或数据丢失的风险。 由于大多数生产工作负荷运行超过三个副本、超过三个容错域，以及每个容错域的多个有效节点，因此默认设置是不需要域分配。 这样标准平衡和故障转移便可以处理这些情况，即使这意味着，暂时某个域可能有多个副本打包到它。

如果要对给定工作负荷禁用此类打包，可以在服务中指定“RequireDomainDistribution”策略。 设置此策略后，群集资源管理器可确保同一容错域或升级域中不允许有同一分区的两个副本。

代码：

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

现在，是否可针对未跨越地理区域的群集中的服务使用这些配置？ 当然可以！ 但也没有充分的理由。 除非你实际上正在运行跨越地理距离的群集，否则，应避免使用所需、无效和首选域配置。 尝试强制给定工作负荷在单个机架中运行，或优先选择本地群集的某段没有任何意义。 应在域之间分布不同的硬件配置，并通过标准放置约束和节点属性处理这些配置。

## <a name="next-steps"></a>后续步骤
* 有关可用于配置服务的其他选项的详细信息，请转到[了解如何配置服务](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


