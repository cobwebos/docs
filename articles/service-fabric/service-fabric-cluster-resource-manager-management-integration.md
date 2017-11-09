---
title: "Service Fabric 群集Resource Manager - 管理集成 | Microsoft 文档"
description: "概述群集 Resource Manager 与 Service Fabric 管理之间的集成点。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 70c0cc37a1d362c937ab86bd630c5ab051e63870
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>群集 Resource Manager 与 Service Fabric 群集管理的集成
Service Fabric 群集资源管理器不会在 Service Fabric 中驱动升级，但会关注升级。 群集 Resource Manager 帮助进行管理的第一种方式是跟踪群集及其中服务的所需状态。 无法将群集放入所需配置时，群集 Resource Manager 会发出运行状况报告。 例如，如果容量不足，则群集资源管理器会发出运行状况警告和错误，指示该问题。 集成的另一个部分与升级的工作方式有关。 在升级期间，群集资源管理器会稍微改变其行为。  

## <a name="health-integration"></a>运行状况集成
群集资源管理器会持续跟踪为放置服务而定义的规则。 还会将节点上和群集中的每个指标作为一个整体，跟踪其剩余容量。 如果不满足这些规则或者容量不足，则会发出运行状况警告和错误。 例如，如果某个节点超出容量，Resource Manager 将尝试通过移动服务来修复这种问题。 如果无法纠正，群集资源管理器将发出运行状况警告，指出哪个节点超出容量，以及警告是针对哪些指标。

Resource Manager 发出运行状况警告的另一个示例是发生了放置约束违规情况。 例如，如果已定义放置约束（如 `“NodeColor == Blue”`），而资源管理器检测到违反该约束的情况，则会发出运行状况警告。 这一点适用于自定义约束和默认约束（例如容错域和升级域约束）。

下面是此类运行状况报告的示例。 在这种情况下，运行状况报告适用于系统服务的分区之一。 运行状况消息指出，该分区的副本临时打包到了过少的升级域。

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

下面是此运行状况消息指出的情况：

1. 所有副本本身处于正常状态：每个副本的 AggregatedHealthState 均为正常
2. 当前违反了升级域分发约束。 这表示特定的升级域在此分区中拥有的副本数超出了预期。
3. 哪些节点包含会引起违规的副本。 在这种情况下，是名为“Node.8”的节点
4. 此分区中是否正在进行升级（“当前正在升级 -- false”）
5. 此服务的分发策略：“分发策略 -- 打包”。 这受`RequireDomainDistribution`[放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)管制。 “打包”指示在此情况下不需要 DomainDistribution，从而使我们知道未对此服务指定放置策略。 
6. 报告发生时间 -- 2015/8/10 晚上 7:13:02

此类信息丰富了生产环境中触发的警报，可让用户知道某个地方出错了，还可用于检测和暂停错误升级。 在此情况下，我们可以调查 Resource Manager 为何必须将副本打包到升级域。 例如，打包通常是暂时的，因为其他升级域中的节点已关闭。

假设群集资源管理器正尝试放置某些服务，但没有任何可行的解决方案。 如果不能放置服务，通常是由于以下原因之一引起的：

1. 某个暂时性状态导致无法正确放置此服务实例或副本
2. 不满足服务的放置要求。

在这些情况下，群集资源管理器的运行状况报告可帮助确定不能放置服务的原因。 我们将此过程称为“约束消除序列”。 在此过程中，系统将逐步了解配置的约束如何影响服务，并记录约束消除的因素。 这样，当无法放置服务时，便可以看到哪些节点已被消除及其原因。

## <a name="constraint-types"></a>约束类型
接下来，我们讨论一下这些运行状况报告中的各种约束。 不能放置副本时，将看到与这些约束相关的运行状况消息。

* ReplicaExclusionStatic 和 ReplicaExclusionDynamic：这些约束指示系统拒绝某解决方案是由于同一分区中的两个服务对象必须放置在同一节点上。 不允许这样操作，因为该节点的失败会过度地影响该分区。 ReplicaExclusionStatic 和 ReplicaExclusionDynamic 遵循几乎相同的规则，有所差别也无关紧要。 如果看到的约束消除序列包含 ReplicaExclusionStatic 或 ReplicaExclusionDynamic 约束，群集资源管理器就会认为没有足够的节点。 这要求剩余的解决方案能够使用这些不允许使用的无效放置。 序列中的其他约束通常会告诉我们首先要消除节点的原因。
* **PlacementConstraint**：如果看到此消息，表示已消除了一些节点，因为它们不符合服务的放置约束。 我们在此消息中描绘当前配置的放置约束。 如果定义了放置约束，则这种情况是正常的。 但是，如果放置约束错误地导致消除了过多的节点，则会看到这种结果。
* NodeCapacity：此约束表示群集资源管理器无法将副本放在指定的节点上，因为这样放置会超出容量。
* **Affinity**：此约束表示无法将副本放在受影响的节点上，因为这会导致违反相关性约束。 [此文](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)介绍了有关相关性的详细信息。
* **FaultDomain** 和 **UpgradeDomain**：如果将副本放在指定的节点上会导致副本打包在特定的容错域或升级域中，此约束将消除节点。 [容错域与升级域约束及最终行为](service-fabric-cluster-resource-manager-cluster-description.md)中的主题提供了几个介绍此约束的示例
* PreferredLocation：通常我们看不到这个会将节点从解决方案中删除的约束，因为该约束默认作为优化运行。 首选的位置约束还会出现在升级期间。 在升级期间，该约束用于将服务移回到开始升级时所在的位置。

## <a name="blocklisting-nodes"></a>将节点列入阻止列表
群集资源管理器报告的另一个运行状况消息是节点何时列入阻止列表。 可以将列入阻止列表看作自动应用的临时约束。 如果节点在启动该服务类型的实例时遇到重复的失败，则会将这些节点列入阻止列表。 根据每个服务类型，将节点列入阻止列表。 系统会由于一种服务类型（非另一种）而将某个节点列入阻止列表。 

会看到通常在开发过程中开始列入阻止列表：一些 bug 会导致服务主机在启动时发生故障。 Service Fabric 多次尝试创建服务主机，但一直发生故障。 几次尝试后，会将该节点列入阻止列表，群集资源管理器会尝试在其他位置创建该服务。 如果该故障在多个节点上发生，则可能最终会将群集中的所有有效节点列入阻止列表。 列入阻止列表还可以移除很多节点，但不会多到可以成功启动服务以满足所需规模。 通常会看到群集资源管理器的其他错误或警告，指示服务低于所需的副本数或实例数，还会看到运行状况消息，指示导致列入阻止列表的首要故障是什么。

列入阻止列表不是永久性条件。 几分钟后，会从阻止列表删除该节点，并且 Service Fabric 可能会再次激活该节点上的服务。 如果服务仍失败，则会再次因该服务类型而将该节点列入阻止列表。 

### <a name="constraint-priorities"></a>约束优先级

> [!WARNING]
> 不建议更改约束优先级，并且可能对群集造成严重的不利影响。 提供以下信息，供默认约束优先级及其行为参考。 
>

在所有约束中，可能觉得：“嘿 – 我认为容错域约束在系统中是最重要的。 为了确保不违反故障域约束，我情愿违反其他约束。”

可为约束配置不同的优先级别。 其中包括：

   - “硬”(0)
   - “软”(1)
   - “优化”(2)
   - “关”(-1)。 
   
大多数约束默认配置为硬约束。

更改约束的优先级并不常见。 有时需要更改约束优先级，通常是为了解决已影响环境的其他 bug 或行为。 一般而言，约束优化级基础结构的弹性可以应对各种问题，但我们并不经常需要利用这种弹性。 在大部分时间内，每个组成部分使用其默认优先级就能正常运作。 

优先级别不表示会违反给定的约束，也不表示会始终满足给定约束。 约束优先级定义强制执行约束的顺序。 优先级定义无法满足所有约束时的折衷方案。 通常可以满足所有约束，除非环境中还有其他要求。 一些将导致约束冲突的方案示例包括违反约束或大量的并发故障。

在某些高级场合中，可以更改约束优先级。 例如，假如希望确保有必要解决节点容量问题时始终违反相关性。 为此，可将相关性约束的优先级设置为“软”(1)，将容量约束保持设置为“硬”(0)。

以下配置文件中指定了不同约束的默认优先级值：

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>容错域和升级域约束
群集资源管理器要保留在容错域和升级域之间分布的服务。 它会将此作为群集资源管理器引擎内的约束进行建模。 有关其具体用法和特定行为的详细信息，请参阅有关[群集配置](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)的文章。

群集资源管理器可能需要将一些副本打包到升级域，处理升级、故障或其他约束违规。 通常，仅当系统中的多种故障或其他扰乱因素阻止正确放置时，才会打包到故障或升级域。 若要在这些情况下防止打包，可以利用`RequireDomainDistribution`[放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)。 请注意，作为副作用，这可能会影响服务可用性和可靠性，因此请仔细考虑。

如果已正确配置环境，则即使在升级期间，也会完全遵循所有约束。 关键的一点是，群集资源管理器会自动监视约束。 检测到违规时，它会立即报告并尝试解决问题。

## <a name="the-preferred-location-constraint"></a>首选位置约束
PreferredLocation 约束稍有不同，因为它具有两种不同的用法。 此约束的一个用法是在应用程序升级过程中使用。 群集资源管理器在升级过程中自动管理此约束。 它用于确保升级完成时该副本返回到其初始位置。 PreferredLocation 约束的另一种用法是用于[`PreferredPrimaryDomain`放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)。 这两种用法都是优化，因此 PreferredLocation 约束是唯一默认设置为“Optimization”的约束。

## <a name="upgrades"></a>升级
在应用程序和群集升级期间，群集 Resource Manager 也会提供帮助，在此过程中它会执行两个作业：

* 确保群集的规则不受到影响
* 尝试帮助升级顺利完成

### <a name="keep-enforcing-the-rules"></a>保持强制实施规则
规则是需要注意的重点 – 在升级期间仍强制实施严格的约束（如放置约束和容量）。 放置约束确保工作负载仅在受允许的情况下才能运行，即使在升级期间也是如此。 对服务采取高度约束时，升级可能需要更长时间。 服务或运行服务的节点因某更新而关闭时，关于服务的运行位置，有以下几个选项。

### <a name="smart-replacements"></a>智能替换
开始升级时，Resource Manager 将创建当前群集排列方式的快照。 每个升级域完成后，会尝试将该升级域中的服务恢复到其原始排列方式。 这样，一个服务在升级过程中最多可以有两次转换。 一次是从受影响的节点移出，一次是移入。 将群集或服务恢复到升级前的状态还可确保升级不会对群集的布局造成影响。 

### <a name="reduced-churn"></a>降低流动
升级期间还会发生另一种情况，那就是群集资源管理器关闭均衡。 阻止均衡可防止对升级本身做出不必要的反应，例如，为了升级而将服务移入空节点。 如果有问题的升级是群集升级，则整个群集在升级期间会失衡。 约束检查一直处于活动状态，仅禁用基于主动指标均衡的移动。

### <a name="buffered-capacity--upgrade"></a>缓冲容量与升级
通常，即使群集受到约束或接近完整，我们还是希望升级能够完成。 在升级期间管理群集容量的能力比平常时候更为重要。 根据升级域的数目，当在群集中进行升级时，必须迁移 5% 到 20% 的容量。 工作负荷必须转到某个位置。 这就是[缓冲容量](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)概念派上用场的地方。 在正常操作过程中需要考虑缓冲容量。 如有必要，在升级过程中，群集资源管理器可能会将节点填充到其总容量（占用缓冲区）。

## <a name="next-steps"></a>后续步骤
* 从头开始并[获取 Service Fabric 群集 Resource Manager 简介](service-fabric-cluster-resource-manager-introduction.md)
