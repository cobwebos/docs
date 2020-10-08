---
title: Service Fabric 群集容量规划注意事项
description: 在规划 Service Fabric 群集时要考虑节点类型、持久性、可靠性和其他事项。
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: d2b303c22eea9fb46a68bb3c8e36991d47d61554
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817732"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 群集容量规划注意事项

群集容量计划对于每个 Service Fabric 生产环境都非常重要。 需考虑的关键因素包括：

* 群集节点类型的初始编号和属性

* 每个节点类型的持续性级别，它决定 Azure 基础结构中的 Service Fabric VM 权限

* 群集的可靠性级别，它决定 Service Fabric 系统服务的稳定性及整体集群功能

本文将引导你了解其中每个方面的重要决策点。

## <a name="initial-number-and-properties-of-cluster-node-types"></a>群集节点类型的初始编号和属性

节点类型定义群集中一组节点（虚拟机）的大小、数量和属性。 在 Service Fabric 群集中定义的每个节点类型映射到[虚拟机规模集](../virtual-machine-scale-sets/overview.md)。

由于每个节点类型是不同的规模集，所以可以独立纵向扩展或缩减，可以打开不同的端口集，并使用不同的容量指标。 有关节点类型和虚拟机规模集之间关系的详细信息，请参阅 [Service Fabric 群集节点类型](service-fabric-cluster-nodetypes.md)。

每个群集都需要一个主节点类型，它运行提供 Service Fabric 平台功能的关键系统服务。 虽然也可以使用主节点类型来运行应用程序，但建议仅将其用于运行系统服务。

非主节点类型可用于定义应用程序角色（例如前端和后端服务）并在群集中物理隔离服务 。 Service Fabric 群集可以有零个或更多非主节点类型。

使用 Azure 资源管理器部署模板中节点类型定义下的 `isPrimary` 属性配置主节点类型。 有关节点类型属性的完整列表，请参阅 [NodeTypeDescription 对象](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)。 若要了解示例用法，请打开 [Service Fabric 群集示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/)中的任意 AzureDeploy.json 文件，并通过“在页面上查找”来搜索 `nodeTypes` 对象 。

### <a name="node-type-planning-considerations"></a>主节点计划注意事项

初始节点类型的数量取决于集群的目的以及集群上运行的应用程序和服务。 考虑以下问题：

* ***应用程序是否有多个服务，其中是否有任何服务需面向公众或面向 Internet？***

    典型的应用程序包括从客户端接收输入的前端网关服务，以及与前端服务进行通信的一个或多个后端服务，前端和后端服务之间单独联网。 这些情况通常需要三种节点类型：一个主节点类型和两个非主节点类型（分别用于前端和后端服务）。

* ***构成应用程序的各项服务是否有不同的基础结构要求，例如更多的 RAM 或更高的 CPU 周期？***

    前端服务通常可以在容量较小（如 D2 等 VM 大小）且向 Internet 开放了端口的 VM 上运行。  计算密集型后端服务可能需要在不面向 Internet 的大型 VM（D4、D6、D15 等 VM 大小）上运行。 为这些服务定义不同的节点类型，可以更有效、更安全地使用基础 Service Fabric VM，并使它们能够独立缩放。 有关估算所需资源量的详细信息，请参阅 [Service Fabric 应用程序的容量计划](service-fabric-capacity-planning.md)

* ***是否有应用程序服务需要扩展到 100 个节点以上？***

    对于 Service Fabric 应用程序，单个节点类型无法可靠地扩展到每个虚拟机规模集 100 个节点以上。 运行超过 100 个节点需要额外的虚拟机规模集（因而还需要其他节点类型）。

* ***你的群集是否跨越可用性区域？***

    Service Fabric 通过部署固定到特定区域的节点类型来支持跨 [可用性区域](../availability-zones/az-overview.md) 的群集，确保应用程序的高可用性。 可用性区域需要其他节点类型规划和最低要求。 有关详细信息，请参阅 [跨可用性区域 Service Fabric 群集的主节点类型的建议拓扑](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones)。 

为集群的初始创建确定节点类型的数量和属性时，请记住，部署集群后，随时可以添加、修改或删除（非主要）节点类型。 也可以在正在运行的集群中[修改主节点类型](service-fabric-scale-up-primary-node-type.md)（尽管在生产环境中执行此类操作需要大量的计划和谨慎工作）。

节点类型属性的另一个考虑因素是持续性级别，它决定该节点类型的 VM 在 Azure 基础结构中拥有的权限。 使用你为群集选择的 VM 的大小以及为各个节点类型分配的实例计数，帮助确定每种节点类型的适当持续性层，如下所述。

## <a name="durability-characteristics-of-the-cluster"></a>群集的持续性特征

持续性级别指定 Service Fabric VM 对底层 Azure 基础结构的权限。 此特权允许 Service Fabric 暂停任何会影响 Service Fabric 系统服务和你的有状态服务的法定要求的 VM 级基础结构请求（例如重新引导、重新映像或迁移）。

> [!IMPORTANT]
> 为每个节点类型设置持续性级别。 如果未指定任何级别，将使用青铜层，但该层不提供自动 OS 升级。 对于生产工作负载，建议使用白银或黄金级持续性 。

下表列出了 Service Fabric 持续性层级及其要求和可提供的能力。

| 持续性层  | 所需 VM 数量下限 | 支持的 VM 大小                                                                  | 你对虚拟机规模集所做的更新                               | Azure 启动的更新和维护                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | 专用于单个客户的全节点大小 (例如，L32s、GS5、G5、DS15_v2 D15_v2)  | 可延迟到 Service Fabric 群集批准 | 可以在每个升级域中暂停 2 个小时，以留出更多时间使副本从早期故障中恢复 |
| Silver           | 5                              | 单核或更多核心的 VM，至少 50 GB 的本地 SSD                      | 可延迟到 Service Fabric 群集批准 | 任何时候都无法延迟                                                    |
| Bronze          | 1                              | VM，至少 50 GB 的本地 SSD                                              | 不会因为 Service Fabric 群集延迟           | 任何时候都无法延迟                                                    |

> [!WARNING]
> 在青铜级持续性下，无法进行自动 OS 映像升级。 虽然不推荐将[补丁协调应用程序](service-fabric-patch-orchestration-application.md)（仅适用于非 Azure 托管群集）用于白银或更高持续性级别，但对于 Service Fabric 升级域，只能通过它自动执行 Windows 更新。

> [!IMPORTANT]
> 无论在任何持续性级别，VM 规模集上的[解除分配](/rest/api/compute/virtualmachinescalesets/deallocate)操作都将破坏群集。

### <a name="bronze"></a>Bronze

以青铜级持续性运行的节点类型不具有任何特权。 这意味着不会停止或延迟影响你的有状态工作负载的基础结构作业。 对仅运行无状态工作负载的节点类型使用青铜级持续性。 对于生产工作负荷，建议运行“白银”或以上级别。

### <a name="silver-and-gold"></a>白银和黄金

对于承载你希望经常扩展的有状态服务的所有节点类型，以及希望在其中延迟部署操作并减少容量的位置来简化流程，请对所有节点类型使用白银或黄金级持续性。 横向扩展方案不应影响你对持续性层级的选择。

#### <a name="advantages"></a>优点

* 减少横向缩减操作所需的步骤数（自动调用节点停用和 Remove-ServiceFabricNodeState）。
* 降低因就地 VM 大小更改操作和 Azure 基础结构操作而导致数据丢失的风险。

#### <a name="disadvantages"></a>缺点

* 群集中或基础结构级别的问题可能使部署到虚拟机规模集和其他相关 Azure 资源的操作超时、延迟或完全受阻。
* 由于 Azure 基础结构操作期间发生的自动节点停用而增加了[副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，主交换）的数量。
* 执行 Azure 平台的软件更新或硬件维护活动时，将节点暂停服务一段时间。 在这些活动期间，你可能会看到状态为“正在禁用”/“已禁用”的节点。 这会暂时降低群集的容量，但不应影响群集或应用程序的可用性。

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>白银和黄金级持续性节点类型的最佳做法

请遵循以下建议来管理具有白银或黄金持续性的节点类型：

* 使群集和应用程序在任何时间都正常工作，并确保应用程序及时响应所有[服务副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，生成副本时出现停滞）。
* 采用更安全的方式进行 VM 大小更改（纵向扩展/缩减）。 要更改虚拟机规模集的 VM 大小，需要仔细计划和谨慎操作。 有关详细信息，请参阅[纵向扩展 Service Fabric 节点类型](service-fabric-scale-up-primary-node-type.md)
* 为任何已启用“黄金”或“白银”耐久性级别的虚拟机规模集保留至少五个节点。 如果横向缩减到此阈值以下，群集将进入错误状态，需要手动清除已删除节点的状态 (`Remove-ServiceFabricNodeState`)。
* 持续性级别为“白银”或“黄金”的每个虚拟机规模集，在 Service Fabric 群集中都必须映射到其自己的节点类型。 将多个虚拟机规模集映射到单个节点类型，将阻碍 Service Fabric 群集和 Azure 基础结构间的协调正常工作。
* 不要删除随机 VM 实例，请始终使用虚拟机规模集横向缩减功能。 删除随机 VM 实例可能会在分布于[升级域](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains)和[故障域](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)的 VM 实例中造成不平衡。 这一失衡可能会对系统在服务实例/服务副本之间进行适当负载均衡的能力产生负面影响。
* 如果使用自动缩放，请设置规则，使得在同一时间仅对一个节点进行横向缩减（删除 VM 实例）。 一次减少多个实例是不安全的。
* 如果在主节点类型上删除或取消分配 VM，切勿将已分配 VM 数降至可靠性层所需数量以下。 在耐久性级别为“白银”或“黄金”的规模集中，这些操作会被无限期阻止。

### <a name="changing-durability-levels"></a>更改耐久性级别

在某些约束下，可以调整节点类型的持续性级别：

* 具有白银或黄金持续性级别的节点类型不能降级到青铜级。
* 从“青铜”升级到“白银”或“黄金”可能需要几个小时。
* 更改持续性级别时，请务必在虚拟机规模集资源中的 Service Fabric 扩展配置以及 Service Fabric 群集资源中的节点类型定义中同时进行更新。 这些值必须匹配。

容量计划的另一个注意事项是群集的可靠性级别，它决定系统服务和整个群集的稳定性，如以下部分所述。

## <a name="reliability-characteristics-of-the-cluster"></a>群集的可靠性特征

群集可靠性级别决定在群集主节点类型上运行的系统服务副本数。 副本越多，系统服务（以及整个集群）越可靠。

> [!IMPORTANT]
> 在集群级别设置可靠性级别，它决定主节点类型的节点数下限。 生产工作负载需要白银（大于或等于 5 个节点）或更高的可靠性级别。  

可靠性层可以采用以下值：

* 白金 - 系统服务在目标副本集计数为 9 的情况下运行
* 黄金 - 系统服务在目标副本集计数为 7 的情况下运行
* 白银 - 系统服务在目标副本集计数为 5 的情况下运行
* 青铜 - 系统服务在目标副本集计数为 3 的情况下运行

下面是有关选择可靠性层的建议。 种子节点数也设置为可靠性层的最小节点数。


| **节点数** | **可靠性层** |
| --- | --- |
| 1 | 请勿指定 `reliabilityLevel` 参数：系统将会计算它。 |
| 3 | Bronze |
| 5 或 6| Silver |
| 7 或 8 | Gold |
| 9 及以上 | Platinum |

当增大或减小群集的大小（所有节点类型中的 VM 实例的总数）时，请考虑提升群集的可靠性层级。 这样做会触发更改系统服务副本集计数所需的群集升级。 等待升级完成，然后对群集做出其他任何更改，例如添加节点。  可以在 Service Fabric Explorer 中运行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) 来监视升级进度

### <a name="capacity-planning-for-reliability"></a>保障可靠性的容量计划

群集的容量需求取决于特定的工作负载和可靠性要求。 本节提供一般指导，帮助你开始进行容量计划。

#### <a name="virtual-machine-sizing"></a>虚拟机大小调整

“对于生产工作负载，建议将 VM 大小 (SKU) 设为 [Standard D2_V2](../virtual-machines/dv2-dsv2-series.md)（或同等规模），并使其具有至少 50 GB 的本地 SSD、2 个核心和 4 GiB 的内存。” 建议至少使用 50 GB 的本地 SSD，但某些工作负载（例如运行 Windows 容器的工作负荷）需要更大的磁盘。 为生产工作负载选择其他 [VM 大小](../virtual-machines/sizes-general.md)时，请记住以下约束：

- 不支持部分核心 VM 大小，例如 Standard A0。
- 由于性能原因，不支持 A系列 VM 大小。
- 不支持低优先级 VM。

#### <a name="primary-node-type"></a>主节点类型

Azure 上的生产工作负载至少需要 5 个主节点（VM 实例）和白银可靠性层。 建议将集群主节点类型专用于系统服务，并使用放置约束将应用程序部署到辅助节点类型。

Azure 中的测试工作负载可以运行至少 1 个或 3 个主节点。 若要配置单节点群集，请确保在资源管理器模板中完全省略 `reliabilityLevel` 设置（指定空字符串作为 `reliabilityLevel` 值是不够的）。 如果使用 Azure 门户设置单节点群集设置，将自动完成此配置。

> [!WARNING]
> 单节点群集以没有可靠性的特殊配置运行，并且不支持横向扩展。

#### <a name="non-primary-node-types"></a>非主节点类型

非主节点类型的节点数下限取决于节点类型的特定[持久性级别](#durability-characteristics-of-the-cluster)。 应根据要为相应节点类型运行的应用程序或服务的副本数，以及工作负载是有状态还是无状态来计划节点数（和持续性级别）。 请记住，部署群集后，可以随时增加或减少节点类型中的 VM 数量。

##### <a name="stateful-workloads"></a>有状态工作负载

对于使用 Service Fabric [可靠集合或可靠参与者](service-fabric-choose-framework.md)的有状态生产工作负载，建议将副本数下限及目标设为 5。 这样一来，在稳定状态下，在每个容错域和升级域中都将有一个副本（来自副本集）。 通常，使用为系统服务设置的可靠性级别来指导用于有状态服务的副本计数。

##### <a name="stateless-workloads"></a>无状态工作负载

对于无状态生产工作负载，为维持仲裁，支持的最小非主节点类型大小为 3，但建议将节点类型大小设为 5。

## <a name="next-steps"></a>后续步骤

在配置集群之前，请查看 `Not Allowed` [集群升级策略](service-fabric-cluster-fabric-settings.md)，以免稍后由于无法通过其他方式更改系统配置设置而必须重新创建集群。

有关群集计划的详细信息，请参阅：

* [计算规划和缩放](service-fabric-best-practices-capacity-scaling.md)
* [Service Fabric 应用程序的容量规划](service-fabric-capacity-planning.md)
* [灾难恢复规划](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
