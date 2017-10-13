---
title: "Azure Service Fabric 灾难恢复 | Microsoft 文档"
description: "Azure Service Fabric 提供所需的功能用于应对各种灾难。 本文介绍可能发生的灾难类型，以及如何应对这些灾难。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5346e331d76149ac3aed7aaf11eb3171e0ac5cfc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure Service Fabric 中的灾难恢复
提供高可用性的关键一环是确保服务能够经受各种不同类型的故障。 对于计划外和不受控制的故障，这一点尤其重要。 本文介绍一些常见的故障模式，如果未正确建模和管理，这些故障可能成为灾难。 本文还介绍发生灾难时应采取的缓解措施和行动。 目标是在发生计划内或其他故障时，限制或消除停机或数据丢失风险。

## <a name="avoiding-disaster"></a>避免灾难
Service Fabric 的主要目标是帮助用户对环境和服务进行建模，使常见故障类型不构成灾难。 

一般而言，有两种类型的灾难/故障方案：

1. 硬件或软件故障
2. 操作故障

### <a name="hardware-and-software-faults"></a>硬件和软件故障
硬件和软件故障是不可预知的。 解决故障的最简单方法是跨硬件或软件故障边界运行服务的多个副本。 例如，如果服务仅在一台特定计算机上运行，则该计算机的故障将是该服务的灾难。 避免此灾难的简单方法是，确保服务实际上在多台计算机上运行。 另外，必须进行测试以确保一台计算机的故障不会中断正在运行的服务。 容量规划确保可以在其他位置创建替换实例，并且容量减少不会使剩余服务过载。 无论尝试避免哪种故障，都可以使用这一相同模式。 例如： 如果担心某一 SAN 发生故障，则可跨多个 SAN 运行。 如果担心服务器机架丢失，则可跨多个机架运行。 如果担心数据中心丢失，则应跨多个 Azure 区域或数据中心运行服务。 

在此类跨区模式下运行时，仍可能发生某些类型的同步故障，但系统会自动处理特定类型的单个甚至多个故障（不包括：单个 VM 或网络链接故障），且这些故障不再是“灾难”。 Service Fabric 提供许多用于扩展群集的机制，并处理失败节点和服务的返回。 使用 Service Fabric 还可以运行许多服务实例，以避免这些类型的计划外故障变成真正的灾难。

一些原因导致通过运行足够大的部署来跨越故障并不可行。 例如，相对于发生故障的可能性，需要的硬件资源可能超过你愿意支付的数目。 处理分布式应用程序时，不同地理距离之间的附加通信跃点或状态复制成本可能导致不可接受的延迟。 具体界限因不同的应用程序而异。 具体而言，软件故障可能发生在尝试缩放的服务中。 在这种情况下，运行多个副本不能防止灾难发生，因为故障条件与所有实例相关。

### <a name="operational-faults"></a>操作故障
即使服务遍布全球且存在许多冗余，仍然可能遇到灾难性事件。 例如，如果有人意外重新配置或彻底删除了服务的 DNS 名称。 举例来说，假设你有一个有状态 Service Fabric 服务，有人意外删除了该服务。 除非有一些其他的缓解措施，否则该服务及其所有状态现在就不复存在了。 这些类型的操作灾难（“事故”）需要采取不同于常规计划外故障的缓解措施和步骤才能恢复。 

避免这些类型的操作故障的最佳方法是：
1. 限制对环境的操作访问
2. 严格审核危险操作
3. 强制实行自动化、防止手动或带外更改，并在执行特定更改之前针对实际环境对其进行验证
4. 确保破坏性操作为“软”。 软操作不会立即生效，或者可以在一定的时间范围内撤消

Service Fabric 提供了用于防止操作故障的一些机制，例如提供针对群集操作的[基于角色](service-fabric-cluster-security-roles.md)的访问控制。 但是，大多数操作故障都需要组织和其他系统来配合解决。 Service Fabric 提供了一些机制来解决操作故障，最值得注意的是有状态服务的备份和还原。

## <a name="managing-failures"></a>管理故障
Service Fabric 的目标几乎始终是自动管理故障。 但是，若要处理某些类型的故障，服务必须具有其他代码。 出于安全性和业务连续性的考虑，不应自动处理其他类型的故障。 

### <a name="handling-single-failures"></a>处理单个故障
单台计算机可能由于各种原因发生故障。 其中一些原因是硬件问题，例如电源和网络硬件故障。 其他故障与软件有关。 例如，实际操作系统和服务本身的故障。 Service Fabric 会自动检测这些类型的故障，包括由于网络问题导致某一计算机与其他计算机相隔离的情况。

无论服务是什么类型，如果代码的单个副本由于任何原因而发生故障，则运行单个实例会导致该服务停机。 

若要处理任何单一故障，可执行的最简单操作是确保服务默认在多个节点上运行。 对于无状态服务，可通过将 `InstanceCount` 设置为大于 1 来实现这一点。 对于有状态服务，最低建议是始终将 `TargetReplicaSetSize` 和 `MinReplicaSetSize` 设置为不小于 3。 运行服务代码的更多副本可确保服务能够自动处理任何单一故障。 

### <a name="handling-coordinated-failures"></a>处理协调性故障
由于计划内或计划外基础结构故障和更改，或计划内软件更改，群集中可能发生协调性故障。 Service Fabric 将遇到协调性故障的基础结构区域建模为容错域。 将遇到协调性软件更改的区域建模为升级域。 若要详细了解容错域和升级域，请参阅[本文档](service-fabric-cluster-resource-manager-cluster-description.md)，其中描述了群集拓扑和定义。

默认情况下，Service Fabric 会在规划服务应该运行的位置时考虑容错域和升级域。 默认情况下，Service Fabric 尝试确保服务跨多个容错域和升级域运行，以便发生计划内或计划外更改时，服务仍然可用。 

例如，假设电源故障导致计算机机架同时发生故障。 运行多个服务副本时，容错域中丢失多台计算机的故障会变成给定服务单一故障的另一个示例。 因此，管理容错域对于确保服务高可用性至关重要。 在 Azure 中运行 Service Fabric 时，系统会自动管理容错域。 在其他环境中，可能不是这样。 若要在本地生成自己的群集，请务必正确映射和规划容错域布局。

升级域可用于针对软件同时升级的区域进行建模。 因此，升级域通常还可以定义在计划升级期间停用软件的边界。 Service Fabric 和服务的升级均遵循相同的模型。 若要详细了解滚动升级、升级域和有助于防止意外更改影响群集和服务的 Service Fabric 运行状况模型，请参阅以下文档：

 - [应用程序升级](service-fabric-application-upgrade.md)
 - [应用程序升级教程](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric 运行状况模型](service-fabric-health-introduction.md)

可以使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 中提供的群集映射来可视化群集布局：

<center>
![Service Fabric Explorer 中分散在容错域之间的节点][sfx-cluster-map]
</center>

> [!NOTE]
> 建模故障区域、滚动升级、运行服务代码和状态的多个实例、确保服务在容错域和升级域中运行的放置规则，以及内置运行状况监视仅仅是 Service Fabric 提供的一些功能，目的是防止正常操作问题和故障变成灾难。 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>处理同步硬件或软件故障
上文讨论了单一故障。 如你所见，只需跨容错域和升级域运行代码（和状态）的多个副本，即可轻松处理无状态和有状态服务的单一故障。 也可能发生多个同时随机故障。 这些故障更有可能造成实际灾难。


### <a name="random-failures-leading-to-service-failures"></a>导致服务故障的随机故障
假设服务的 `InstanceCount` 为 5，并且运行这些实例的多个节点均同时发生故障。 Service Fabric 在其他节点上自动创建替换实例，作出响应。 它将持续创建替换实例，直至服务恢复到所需的实例计数。 再举一例，假设有一个 `InstanceCount` 为 -1 的无状态服务，这意味着它会在群集中的全部有效节点上运行。 假设其中一些实例发生故障。 在这种情况下，Service Fabric 会注意到服务未处于所需状态，并尝试在缺少的节点上创建实例。 

对于有状态服务，这种情况取决于服务有无永久性状态。 还取决于服务拥有的副本数以及发生故障的副本数。 请根据以下 3 个阶段确定有状态服务是否发生灾难并对其进行管理：

1. 确定有无仲裁丢失
 - 仲裁丢失是指有状态服务的大多数副本（包括主要副本）同时发生故障的情况。
2. 确定仲裁丢失是否具有永久性
 - 大多数情况下，故障是暂时性的。 重启进程、节点和 VM，网络分区修复。 但是，故障有时是永久性的。 
    - 对于没有永久性状态的服务，仲裁或多个副本故障会立即导致永久性仲裁丢失。 Service Fabric 在有状态、非永久性服务中检测到仲裁丢失时，会立即通过声明（潜在的）数据丢失转到步骤 3。 转到数据丢失是有意义的，因为 Service Fabric 知道等待副本恢复没有任何意义，这是因为即使副本恢复，也将是空副本。
    - 对于有状态、永久性服务，仲裁或多个副本故障会导致 Service Fabric 开始等待副本恢复并还原仲裁。 这会对服务的受影响分区（或“副本集”）的任何写入造成服务中断。 但是，仍有可能可以读取，其一致性保证降低。 Service Fabric 等待仲裁恢复的默认时间是无限的，因为处理是（潜在的）数据丢失事件并伴有其他风险。 可以替代默认的 `QuorumLossWaitDuration` 值，但不建议这样做。 此时应该尽量还原发生故障的副本。 这需要备份发生故障的节点，并确保其可以将驱动器重新装载到存储本地永久性状态的位置。 如果仲裁丢失由进程故障导致，则 Service Fabric 会自动尝试重新创建进程并重启其中的副本。 如果失败，Service Fabric 会报告运行状况错误。 如果能够解决这些问题，副本通常可以恢复。 但是，副本有时不能恢复。 例如，驱动器可能全部发生故障，或者计算机由于某种原因遭到物理破坏。 这些情况属于永久性仲裁丢失事件。 若要指示 Service Fabric 停止等待发生故障的副本恢复，群集管理员必须确定哪些服务的哪些分区受到了影响，并调用 `Repair-ServiceFabricPartition -PartitionId` 或 ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API。  使用此 API 可以指定分区 ID，使其从仲裁丢失转为潜在的数据丢失。

> [!NOTE]
> 为了确保安全，请务必针对特定分区有目标地使用此 API。 
>

3. 确定有无实际数据丢失，并从备份还原
  - 如果 Service Fabric 调用 `OnDataLossAsync` 方法，这始终是因为疑似数据丢失。 Service Fabric 可确保将此调用传送到最合适的剩余副本。 也就是进度最大的副本。 我们总是将其称为疑似数据丢失，这是因为剩余副本在发生故障时实际上可能与主要副本具有完全相同的状态。 但是，如果没有该状态作为对比，Service Fabric 或操作者就没有很好的方法来明确这一点。 此时，Service Fabric 还知道其他副本不会恢复。 这是当我们停止等待仲裁丢失自行解决时所做的决策。 服务采取的最佳做法通常是冻结并等待特定的管理员介入。 那么 `OnDataLossAsync` 方法所执行的典型实现是什么？
  - 首先，记录 `OnDataLossAsync` 已被触发，并发出任何必要的管理警报。
   - 此时，通常需要暂停并等待进一步决策和要采取的手动操作。 这是因为即使备份可用，也可能需要时间准备。 例如，如果两个不同的服务协调信息，则可能需要修改这些备份，以确保发生还原后，这两个服务所关注的信息一致。 
  - 通常还有一些其他遥测或服务消耗。 此元数据可能包含在其他服务或日志中。 此信息可用于确定主要副本是否收到并处理了任何调用，这些调用未存在于备份中或未复制到此特定副本中。 可能需要重播或向备份添加这些调用才能进行恢复。  
   - 将剩余副本的状态与任何可用备份中所含的状态进行比较。 如果使用 Service Fabric 可靠集合，则可使用工具和进程执行此操作，如[本文](service-fabric-reliable-services-backup-restore.md)所述。 目标是查看副本中的状态是否充足，或查看副本可能丢失的内容。
  - 完成比较以及必要的还原后，如果更改了任何状态，则服务代码应返回 true。 如果副本确定它是该状态的最佳可用副本，并且未执行任何更改，则返回 false。 True 表示任何其他剩余副本现在可能与此副本不一致。 应删除这些副本并根据此副本重新生成。 False 表示未进行任何状态更改，因此其他副本可以保留其所含内容。 

在生产环境中部署服务之前，服务创建者务必对潜在的数据丢失和故障方案进行实践。 为了防止数据丢失的可能性，务必定期将任何有状态服务的[状态备份](service-fabric-reliable-services-backup-restore.md)到异地冗余存储。 还需确保能够将其还原。 由于许多不同服务进行备份的时间有所不同，因此需要确保还原后，各个服务具有相互一致的视图。 例如，考虑这样一种情况：某一服务生成并存储一个数值，然后将其发送给同样存储该数值的另一服务。 还原后，可能发现第二个服务含有该数值但第一个没有，因为其备份未包含该操作。

如果发现剩余副本不足以在数据丢失方案中继续运行，并且不能通过遥测或消耗重构服务状态，则备份频率决定最可行的恢复点目标 (RPO)。 Service Fabric 提供许多工具以测试各种故障方案，包括需要从备份还原的永久性仲裁和数据丢失。 这些方案都包括在 Service Fabric 的可测试工具中，由故障分析服务管理。 有关这些工具和模式的详细信息，请参阅[此处](service-fabric-testability-overview.md)。 

> [!NOTE]
> 系统服务也会遭受仲裁丢失，同时对相关服务造成特定影响。 例如，命名服务的仲裁丢失会影响名称解析，而故障转移管理器服务的仲裁丢失会阻止创建新服务与故障转移。 虽然 Service Fabric 系统服务采用与状态管理服务相同的模式，但不建议尝试将其从仲裁丢失转为潜在的数据丢失。 相反，建议[寻求支持](service-fabric-support.md)来确定针对具体情况的解决方案。  通常最好是单纯地等待，直到关闭的副本恢复为止。
>

## <a name="availability-of-the-service-fabric-cluster"></a>Service Fabric 群集的可用性
一般来说，Service Fabric 群集本身是一个高度分布式环境，并且没有任何单一故障点。 任何一个节点故障不会为群集造成可用性或可靠性问题，主要是因为 Service Fabric 系统服务遵循前面提供的相同准则：默认情况下，它们始终运行 3 个或以上副本，并且这些无状态系统服务在所有节点上运行。 基础 Service Fabric 网络和故障检测层是完全分布式层。 大多数系统服务可以根据群集中的元数据重新生成，或者知道如何从其他位置重新同步其状态。 如果系统服务遇到如上所述的仲裁丢失情况，则群集可用性可能会受到影响。 在这些情况下，可能无法在群机上执行某些操作（例如启动升级或部署新服务），但群集本身仍处于启动状态。 已在运行的服务将继续在这些情况下运行，除非它们要求写入系统服务才能继续运行。 例如，如果故障转移管理器处于仲裁丢失状态，则所有服务将继续运行，但失败的任何服务无法自动重启，因为这需要故障转移管理器的介入。 

### <a name="failures-of-a-datacenter-or-azure-region"></a>数据中心或 Azure 区域的故障
在极少数情况下，物理数据中心可能会由于电源或网络连接中断而暂时不可用。 在这些情况下，Service Fabric 群集和该数据中心或 Azure 区域中的服务不可用。 但会保留你的数据。 对于在 Azure 中运行的群集，可以在 [Azure 状态页][azure-status-dashboard]上查看有关中断的最新信息。 物理数据中心遭到部分或完全损坏（这种情况很少见）时，托管在此处的任何 Service Fabric 群集或其中的服务可能会丢失。 这包括未在该数据中心或区域外部备份的任何状态。

可通过两种不同的策略解决单个数据中心或区域出现的永久性或持续性故障。 

1. 在多个此类区域中运行单独的 Service Fabric 群集，并利用某些机制在这些环境之间进行故障转移和故障回复。 这种多群集、主动-主动或主动-被动模型需要额外的管理工作和操作代码。 这还需要协调来自同一数据中心或区域中服务的备份，以便某一数据中心或区域失败时，其他数据中心或区域可以使用该备份。 
2. 运行跨多个数据中心或区域的单个 Service Fabric 群集。 支持的最低配置是 3 个数据中心或区域。 建议的区域或数据中心数量为 5。 这需要更复杂的群集拓扑。 但是，此模型的好处是一个数据中心或区域的故障可从灾难转变为正常故障。 可通过适用于单个区域内群集的机制来处理这些故障。 容错域、升级域和 Service Fabric 放置规则可确保工作负荷得以分散，使其能够承受正常故障。 若要详细了解有助于在此类群集中运行服务的策略，请仔细阅读[放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>随机故障导致群集故障
Service Fabric 具有种子节点的概念。 种子节点可以维护基础群集的可用性。 这些节点有助于通过在某些类型的网络故障期间，与其他节点建立租约并充当决胜属性来确保群集保持启动状态。 如果随机故障删除了群集中的大部分种子节点并且无法将其恢复，则群集会自动关闭。 在 Azure 中，系统自动管理种子节点：种子节点可分布在可用的容错域和升级域上，如果从群集中删除了单个种子节点，系统会在原位重新创建一个。 

在独立 Service Fabric 群集和 Azure 中，“主节点类型”是运行种子的节点。 定义主节点类型时，Service Fabric 自动利用通过创建最多 9 个种子节点和每个系统服务的 9 个副本所提供的节点数。 如果一组随机故障同时使用大部分系统服务副本，则系统服务将进入如上所述的仲裁丢失状态。 如果大部分种子节点丢失，群集将在不久之后关闭。

## <a name="next-steps"></a>后续步骤
- 了解如何使用[可测试性框架](service-fabric-testability-overview.md)模拟各种故障
- 阅读有关灾难恢复和高可用性的其他资源。 Microsoft 已发布大量有关这些主题的指导。 尽管其中有些文档提到其他产品中使用的特定技术，但也包含了许多可在 Service Fabric 上下文中应用的一般性最佳实践：
  - [可用性清单](../best-practices-availability-checklist.md)
  - [执行灾难恢复演练](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Azure 应用程序的灾难恢复和高可用性][dr-ha-guide]
- 了解 [Service Fabric 支持选项](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
