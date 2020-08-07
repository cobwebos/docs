---
title: Service Fabric 群集容量规划注意事项
description: 在规划 Service Fabric 群集时要考虑的节点类型、持久性、可靠性和其他事项。
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 4949a83ac2aac664c19be46a367fce2bbff4cb02
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904813"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 群集容量规划注意事项

群集容量规划对于每个 Service Fabric 生产环境都非常重要。 关键注意事项包括：

* **群集*节点类型*的初始数量和属性**

* **每个节点类型的*持续*性级别**，它确定 Azure 基础结构中的 Service Fabric VM 权限

* **群集的*可靠性*级别**，决定 Service Fabric 系统服务和总体群集功能的稳定性

本文将指导你完成每个领域的重要决策点。

## <a name="initial-number-and-properties-of-cluster-node-types"></a>群集节点类型的初始数量和属性

*节点类型*定义群集中)  (虚拟机的一组节点的大小、数量和属性。 在 Service Fabric 群集中定义的每个节点类型映射到[虚拟机规模集](../virtual-machine-scale-sets/overview.md)。

由于每个节点类型都是一组不同的规模集，因此可以独立扩展或缩减，打开不同的端口集，并且有不同的容量指标。 有关节点类型与虚拟机规模集之间的关系的详细信息，请参阅[Service Fabric 群集节点类型](service-fabric-cluster-nodetypes.md)。

每个群集都需要一个**主节点类型**，该类型运行提供 Service Fabric 平台功能的关键系统服务。 虽然也可以使用主节点类型来运行应用程序，但建议仅将它们专用于运行系统服务。

可以使用**非主节点类型**定义应用程序角色 (例如*前端*和*后端*服务) ，以及在群集中物理隔离服务。 Service Fabric 群集可以有零个或多个非主节点类型。

主节点类型是使用 `isPrimary` Azure 资源管理器部署模板中节点类型定义下的属性配置的。 有关节点类型属性的完整列表，请参阅[NodeTypeDescription 对象](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)。 例如，在 " [Service Fabric 群集示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/)" 中打开文件中的任何*AzureDeploy.js* ，并在对象的 "搜索"*页上查找* `nodetTypes` 。

### <a name="node-type-planning-considerations"></a>节点类型规划注意事项

初始节点类型的数量取决于你的群集以及在其上运行的应用程序和服务的用途。 考虑以下问题：

* ***应用程序是否有多个服务，其中是否有任何服务需是面向公众或面向 Internet 的服务？***

    典型的应用程序包含一个前端网关服务，该服务接收来自客户端的输入，以及一个或多个与前端服务通信的后端服务，在前端服务与后端服务之间具有不同的网络。 这些情况通常需要三种节点类型：一个主节点类型，两个非主节点类型 (用于前端和后端服务) 。

* ***构成应用程序的服务是否有不同的基础结构需求，例如更多的 RAM 或更高的 CPU 周期？***

    通常，前端服务可以在较小的 Vm 上运行 (VM 大小，如 D2) ，它将端口打开到 internet。  计算密集型后端服务可能需要在 VM 大小 (（例如，不面向 internet 的 D4、D6、D15) ）上运行。 为这些服务定义不同的节点类型可让你更有效、更安全地使用底层 Service Fabric Vm，并使其能够独立缩放。 有关估算所需资源量的详细信息，请参阅[Service Fabric 应用程序的容量规划](service-fabric-capacity-planning.md)

* ***应用程序服务是否需要扩展到超过100个节点？***

    对于 Service Fabric 应用程序，单个节点类型无法可靠地扩展到每个虚拟机规模集的100个节点。 运行超过100个节点需要 (的其他虚拟机规模集，因此) 其他节点类型。

* ***你的群集是否跨越可用性区域？***

    Service Fabric 通过部署固定到特定区域的节点类型来支持跨[可用性区域](../availability-zones/az-overview.md)的群集，确保应用程序的高可用性。 可用性区域需要其他节点类型规划和最低要求。 有关详细信息，请参阅[跨可用性区域 Service Fabric 群集的主节点类型的建议拓扑](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones)。 

确定初始创建群集的节点类型的数量和属性时，请记住，在部署群集后，始终可以添加、修改或删除 (非主) 节点类型。 [主节点类型也可以](service-fabric-scale-up-node-type.md)在正在运行的群集中进行修改 (但此类操作需要在生产环境中进行大量的规划和小心) 。

节点类型属性的进一步注意事项是持久性级别，它确定节点类型的 Vm 在 Azure 基础结构中的特权。 使用为群集选择的 Vm 大小以及为各个节点类型分配的实例计数，以帮助确定每个节点类型的适当持久性层，如下所述。

## <a name="durability-characteristics-of-the-cluster"></a>群集的持久性特征

*持久性级别*指定 Service Fabric vm 与底层 Azure 基础结构的权限。 此特权允许 Service Fabric 暂停任何 VM 级别基础结构请求 (例如，影响 Service Fabric 系统服务和有状态服务的仲裁要求的重新启动、重置映像或迁移) 。

> [!IMPORTANT]
> 为每个节点类型设置了持久性级别。 如果未指定任何内容，则将使用*铜牌*级别，但不会提供自动 OS 升级。 对于生产工作负荷，建议使用 "*白银*" 或 "*黄金*" 持续性。

下表列出了 Service Fabric 持久性层、其要求和实用。

| 持续性层  | 所需 VM 数量下限 | 支持的 VM 大小                                                                  | 你对虚拟机规模集所做的更新                               | Azure 启动的更新和维护                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | 专用于单个客户的全节点大小 (例如，L32s、GS5、G5、DS15_v2 D15_v2)  | 可延迟到 Service Fabric 群集批准 | 每个升级域可暂停2小时，以允许副本额外的时间从以前的故障中恢复 |
| Silver           | 5                              | 单核或更多核心的 VM，至少 50 GB 的本地 SSD                      | 可延迟到 Service Fabric 群集批准 | 任何时候都无法延迟                                                    |
| Bronze          | 1                              | VM，至少 50 GB 的本地 SSD                                              | 不会因为 Service Fabric 群集延迟           | 任何时候都无法延迟                                                    |

> [!WARNING]
> 使用铜牌持续性，自动 OS 映像升级不可用。 虽然[修补业务流程应用程序](service-fabric-patch-orchestration-application.md) (仅适用于非 Azure 托管的群集) 不*建议*用于白银或更高的持久性级别，但它是自动执行 Windows 更新的唯一选项，Service Fabric 升级域。

> [!IMPORTANT]
> 无论持续性级别如何，在虚拟机规模集上运行[解除分配](/rest/api/compute/virtualmachinescalesets/deallocate)操作都将销毁该群集。

### <a name="bronze"></a>Bronze

以青铜级持续性运行的节点类型不具有任何特权。 这意味着，影响有状态工作负荷的基础结构作业不会停止或延迟。 对于仅运行无状态工作负荷的节点类型，请使用铜牌持续性。 对于生产工作负荷，建议运行“白银”或以上级别。

### <a name="silver-and-gold"></a>银和黄金

对于托管你预计会频繁缩放的有状态服务的所有节点类型，以及你希望部署操作延迟并降低容量以简化此过程的情况，请使用白银或金牌持续性。 横向扩展方案不应影响你选择的持久性层。

#### <a name="advantages"></a>优点

* 减少)  (节点停用和删除-Remove-servicefabricnodestate 所需的步骤数。
* 降低由于就地 VM 大小更改操作和 Azure 基础结构操作而导致的数据丢失的风险。

#### <a name="disadvantages"></a>缺点

* 部署到虚拟机规模集和其他相关 Azure 资源可能会超时、延迟，或者完全由群集中的问题或基础结构级别的问题来阻止。
* 由于 Azure 基础结构操作期间发生的自动节点停用而增加了[副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，主交换）的数量。
* 执行 Azure 平台的软件更新或硬件维护活动时，将节点暂停服务一段时间。 在这些活动期间，你可能会看到状态为“正在禁用”/“已禁用”的节点。 这会暂时降低群集的容量，但不应影响群集或应用程序的可用性。

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>银和黄金耐用性节点类型的最佳实践

遵循以下有关管理具有银色或金牌持久性的节点类型的建议：

* 使群集和应用程序在任何时间都正常工作，并确保应用程序及时响应所有[服务副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，生成副本时出现停滞）。
* 采用更安全的方式更改 VM 大小 (增加/减少) 。 更改虚拟机规模集的 VM 大小需要进行周密的规划和警告。 有关详细信息，请参阅[向上缩放 Service Fabric 节点类型](service-fabric-scale-up-node-type.md)
* 为任何已启用“黄金”或“白银”耐久性级别的虚拟机规模集保留至少五个节点。 如果你在低于此阈值的情况下进行缩放，你的群集将进入错误状态，你将需要为 `Remove-ServiceFabricNodeState` 删除的节点手动清理状态 () 。
* 持续性级别为“白银”或“黄金”的每个虚拟机规模集，在 Service Fabric 群集中都必须映射到其自己的节点类型。 将多个虚拟机规模集映射到单个节点类型，将阻碍 Service Fabric 群集和 Azure 基础结构间的协调正常工作。
* 不要删除随机 VM 实例，请始终使用虚拟机规模集横向缩减功能。 删除随机 VM 实例可能会在跨[升级域](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains)和[容错域](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)的 VM 实例中创建失衡。 这种不平衡可能会对系统在服务实例/服务副本之间进行适当负载均衡的能力产生负面影响。
* 如果使用自动缩放，请设置规则，以便 (删除 VM 实例) 操作一次只执行一个节点。 一次减少多个实例是不安全的。
* 如果在主节点类型上删除或解除分配 Vm，请不要在低于可靠性层所需的内容的情况下减少分配的 Vm 的计数。 在耐久性级别为“白银”或“黄金”的规模集中，这些操作会被无限期阻止。

### <a name="changing-durability-levels"></a>更改耐久性级别

在某些约束中，可以调整节点类型持久性级别：

* 持久性级别为银色或金牌的节点类型不能降级为青铜。
* 从“青铜”升级到“白银”或“黄金”可能需要几个小时。
* 更改持久性级别时，请务必在虚拟机规模集资源的 Service Fabric 扩展配置和 Service Fabric 群集资源中的节点类型定义中对其进行更新。 这些值必须匹配。

容量规划的另一个注意事项是群集的可靠性级别，它确定系统服务和总体群集的稳定性，如以下部分所述。

## <a name="reliability-characteristics-of-the-cluster"></a>群集的可靠性特征

群集*可靠性级别*确定群集的主节点类型上运行的系统服务副本数。 副本越多，系统服务 (的可靠性就越多，从而导致群集成为一个整体) 。

> [!IMPORTANT]
> 可靠性级别在群集级别设置并确定主节点类型的最小节点数。 生产工作负荷需要 (大于或等于五个节点) 或更高版本的的可靠性级别。  

可靠性层可以采用以下值：

* **白金**-系统服务运行，其目标副本集计数为9
* **金牌**系统服务运行，目标副本集计数为七个
* **白银**-系统服务运行，目标副本集计数为5
* **青铜**-系统服务运行，其目标副本集计数为三

下面是有关选择可靠性层的建议。 种子节点数也设置为可靠性层的最小节点数。


| **节点数** | **可靠性层** |
| --- | --- |
| 1 | *不要指定 `reliabilityLevel` 参数：系统计算参数。* |
| 3 | Bronze |
| 5 或 6| Silver |
| 7 或 8 | Gold |
| 9 及以上 | Platinum |

增加或减少群集大小 (所有节点类型中的 VM 实例的总和) ，请考虑将群集的可靠性从一个层更新到另一个层。 这样做会触发更改系统服务副本集计数所需的群集升级。 等待升级完成，然后对群集做出其他任何更改，例如添加节点。  可以在 Service Fabric Explorer 中运行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) 来监视升级进度

### <a name="capacity-planning-for-reliability"></a>可靠性规划容量

群集的容量需求取决于特定的工作负荷和可靠性要求。 本部分提供了可帮助你开始进行容量规划的常规指南。

#### <a name="virtual-machine-sizing"></a>虚拟机大小调整

**对于生产工作负荷， (SKU) 的建议 VM 大小为[标准 D2_V2](../virtual-machines/dv2-dsv2-series.md) (或等效) ，最少 50 GB 本地 SSD、2核和 4 GiB 内存。** 建议至少使用 50 GB 本地 SSD，但某些工作负荷 (例如那些运行 Windows 容器) 的工作负荷将需要更大的磁盘。 为生产工作负荷选择其他[VM 大小时](../virtual-machines/sizes-general.md)，请记住以下限制：

- 不支持部分核心 VM 大小（如标准 A0）。
- *A 系列*出于性能原因，不支持 VM 大小。
- 不支持低优先级 VM。

#### <a name="primary-node-type"></a>主节点类型

Azure 上的**生产工作负载**至少需要5个主节点 (VM 实例) 和可靠性层。 建议将群集主节点类型专用于系统服务，并使用放置约束将应用程序部署到辅助节点类型。

Azure 中的**测试工作负荷**可运行至少一个或三个主节点。 若要配置一个节点群集，请确保 `reliabilityLevel` 资源管理器模板中的设置已完全省略 (为指定空字符串值的 `reliabilityLevel`) 不足。 如果设置一个节点群集并 Azure 门户设置，则会自动执行此配置。

> [!WARNING]
> 单节点群集使用没有可靠性的特殊配置运行，不支持向外扩展。

#### <a name="non-primary-node-types"></a>非主节点类型

非主节点类型的最小节点数取决于节点类型的特定[持续性级别](#durability-characteristics-of-the-cluster)。 应根据要为节点类型运行的应用程序或服务的副本数，以及工作负荷是有状态还是无状态，计划 (和持久性级别) 的节点数。 请记住，在部署群集后，可以随时增加或减少节点类型中的 Vm 数量。

##### <a name="stateful-workloads"></a>有状态工作负荷

对于使用 Service Fabric[可靠集合或可靠参与者](service-fabric-choose-framework.md)的有状态生产工作负荷，建议使用最小和目标副本计数5。 为此，在稳定状态下，会从每个容错域和升级域中的副本集)  (副本。 通常，使用为系统服务设置的可靠性级别作为有状态服务使用的副本计数的指南。

##### <a name="stateless-workloads"></a>无状态工作负荷

对于无状态生产工作负荷，支持的最小非主节点类型大小为三个，用于维护仲裁，但建议使用节点类型大小5。

## <a name="next-steps"></a>后续步骤

在配置群集之前，请先查看 `Not Allowed` [群集升级策略](service-fabric-cluster-fabric-settings.md)，以减少以后重新创建群集的时间，因为其他系统配置设置不可配置。

有关群集规划的详细信息，请参阅：

* [计算规划和缩放](service-fabric-best-practices-capacity-scaling.md)
* [Service Fabric 应用程序的容量规划](service-fabric-capacity-planning.md)
* [灾难恢复规划](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
