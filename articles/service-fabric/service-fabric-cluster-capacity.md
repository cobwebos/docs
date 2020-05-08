---
title: 规划 Service Fabric 群集容量
description: Service Fabric 群集容量规划注意事项。 节点类型、操作、耐久性和可靠性层
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: f011dee94e135ba40f8d3c87240e905e4a2739ec
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793051"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 群集容量规划注意事项
对于任何生产部署，容量规划都是一个重要的步骤。 下面是在规划过程中必须注意的一些事项。

* 群集一开始需要的节点类型数目
* 每个节点类型的属性（大小、是否为主节点、是否面向 Internet、VM 数目，等等）
* 群集的可靠性和持久性特征

> [!NOTE]
> 规划期间至少应该查看所有“不允许的”升级策略值  。 这样可以确保正确设置值，并且可以减少不可更改的系统配置设置所导致的群集使用。 
> 

让我们简单地了解其中每一项。

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>群集一开始需要的节点类型数目
首先，需要确定要创建的群集用于什么目的，  以及打算要将哪些类型的应用程序部署到此群集中。 如果不清楚群集的用途，则很可能还未准备好进入容量规划流程。

确定群集一开始需要的节点类型数目。  每个节点类型将映射到虚拟机规模集。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 因此，节点类型数目的确定基本上可归结为以下考虑因素：

* 应用程序是否有多个服务，其中是否有任何服务需面向公众或面向 Internet？ 典型的应用程序包含可接收客户端输入的前端网关服务，以及一个或多个与前端服务通信的后端服务。 因此，在此情况下，必须至少有两个节点类型。
* （构成应用程序的）服务是否有不同的基础结构要求，例如，更多的 RAM 或更高的 CPU 周期？ 例如，假设要部署的应用程序包含前端服务和后端服务。 前端服务可以在容量较小（如 D2 的 VM 大小）且向 Internet 开放了端口的 VM 上运行。  但是，后端服务是计算密集型的服务，需要放在容量较大（D4、D6、D15 等的 VM 大小）且不面向 Internet 的 VM 上运行。
  
  在本示例中，可以决定将所有服务都放在一个节点类型上，但我们建议将它们分别放在群集中的两个节点类型上。  这样，每个节点类型都有不同的属性，例如，Internet 连接或 VM 大小。 也可以单独缩放 VM 的数目。  
* 由于无法预见未来，因此，请利用所知道的事实，选择应用程序一开始需要的节点类型数目。 以后，随时可以添加或删除节点类型。 Service Fabric 群集必须至少有一个节点类型。

## <a name="the-properties-of-each-node-type"></a>每个节点类型的属性
**节点类型** 相当于云服务中的角色。 节点类型定义 VM 大小、VM 数目及其属性。 在 Service Fabric 群集中定义的每个节点类型映射到[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。  
每个节点类型是不同的规模集，可以独立扩展或缩减，可以打开不同的端口集，并且有不同的容量指标。 若要详细了解节点类型和虚拟机规模集之间关系、如何 RDP 到任一实例、如何打开新的端口等，请参阅 [Service Fabric 群集节点类型](service-fabric-cluster-nodetypes.md)。

Service Fabric 群集可以包含不止一个节点类型。 在这种情况下，群集包含一个主节点类型以及一个或多个非主节点类型。

对于 SF 应用程序，单节点类型无法可靠地扩展到每个虚拟机规模集超过 100 个节点；要可靠地实现超过 100 个节点，需要添加其他虚拟机规模集。

### <a name="primary-node-type"></a>主节点类型

Service Fabric 系统服务（例如，群集管理器服务或图像存储服务）是主节点类型。 

![具有两个节点类型的群集的屏幕截图][SystemServices]

* 主节点类型的 **VM 大小下限**取决于选择的**持久性层**。 默认持续性层为“青铜”。 请参阅[群集的持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)以了解更多详细信息。  
* 主节点类型的 **VM 数目下限**取决于选择的**可靠性层**。 默认可靠性层为“白银”。 请参阅[群集的可靠性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)以获取更多详细信息。  

在 Azure 资源管理器模板中，主节点类型在[节点类型定义](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object)下配置了 `isPrimary` 属性。

### <a name="non-primary-node-type"></a>非主节点类型

包含多个节点类型的群集有一个主节点类型，剩余的是非主节点类型。

* 非主节点类型的“VM 大小下限”取决于你选择的“持续性层”   。 默认持续性层为“青铜”。 有关详细信息，请参阅[群集的持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。  
* 非主节点类型的“VM 数量下限”为一  。 但是，应该根据想要在此节点类型中运行的应用程序/服务的副本数目选择此数目。 部署群集之后，节点类型中的 VM 数目可能会增加。

## <a name="the-durability-characteristics-of-the-cluster"></a>群集的持久性特征
持久性层用于向系统指示 VM 对于基本 Azure 基础结构拥有的权限。 在主节点类型中，此权限可让 Service Fabric 暂停影响系统服务及有状态服务的仲裁要求的任何 VM 级别基础结构请求（例如，VM 重启、VM 重置映像或 VM 迁移）。 在非主节点类型中，此特权可让 Service Fabric 暂停影响其中运行的有状态服务的仲裁要求的任何 VM 级别基础结构请求，例如，VM 重新启动、VM 重置映像、VM 迁移，等等。

| 持续性层  | 所需 VM 数量下限 | 受支持的 VM SKU                                                                  | 你对虚拟机规模集所做的更新                               | Azure 启动的更新和维护                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | 专用于单个客户的完整节点 SKU（例如 L32s、GS5、G5、DS15_v2、D15_v2） | 可延迟到 Service Fabric 群集批准 | 每个 UD 可以暂停 2 小时，提供额外的时间让副本从之前的故障中恢复 |
| Silver           | 5                              | 单核或更多核心的 VM，至少 50 GB 的本地 SSD                      | 可延迟到 Service Fabric 群集批准 | 任何时候都无法延迟                                                    |
| Bronze           | 1                              | VM，至少 50 GB 的本地 SSD                                              | 不会因为 Service Fabric 群集延迟           | 任何时候都无法延迟                                                    |

> [!WARNING]
> 以青铜级持续性运行的节点类型不具有任何特权__。 这意味着，不会停止或延迟对有状态工作负荷产生影响的基础结构作业，这可能影响工作负荷。 对仅运行无状态工作负荷的节点类型仅使用“青铜”。 对于生产工作负荷，建议运行“白银”或以上级别。 
> 
> 无论任何持续性级别，VM 规模集上的[释放](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate)操作都将破坏群集

**使用“白银”或“黄金”耐久性级别的优点**
 
- 减少了“缩小规模”操作所需的步骤数（即自动调用节点停用和 Remove-ServiceFabricNodeState）。
- 减少了由于客户启动的就地 VM SKU 更改操作或 Azure 基础结构操作而导致的数据丢失的风险。

**使用“白银”或“黄金”耐久性级别的缺点**
 
- 部署到虚拟机规模集和其他相关 Azure 资源可能会由于群集中的问题或基础结构级别的问题而延迟、超时或被完全阻止。 
- 由于 Azure 基础结构操作期间发生的自动节点停用而增加了[副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，主交换）的数量。
- 执行 Azure 平台的软件更新或硬件维护活动时，将节点暂停服务一段时间。 在这些活动期间，你可能会看到状态为“正在禁用”/“已禁用”的节点。 这会暂时降低群集的容量，但不应影响群集或应用程序的可用性。

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>有关何时使用“白银”或“黄金”持续性级别的建议

对于托管要频繁缩小规模（减小 VM 实例计数）的有状态服务，并且你愿意延迟部署操作并减少容量以利于简化这些“缩小规模”操作的所有节点类型，请使用“白银”或“黄金”耐久性。 “扩大规模”的情况（添加 VM 实例）不会受所选耐久性层的影响，只有“缩小规模”的情况才受其影响。

### <a name="changing-durability-levels"></a>更改耐久性级别
- 耐久性级别为“白银”或“黄金”的节点类型不能降级为“青铜”。
- 从“青铜”升级到“白银”或“黄金”可能需要几个小时。
- 更改耐久性级别时，请务必在虚拟机规模集资源中的 Service Fabric 扩展配置以及 Service Fabric 群集资源中的节点类型定义中同时进行更新。 这些值必须匹配。

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>适用于已设置为“白银”或“黄金”耐久性级别的节点类型的操作建议。

- 使群集和应用程序在任何时间都正常工作，并确保应用程序及时响应所有[服务副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，生成副本时出现停滞）。
- 采用更安全的方式进行 VM SKU 更改（增大/减小）：更改虚拟机规模集的 VM SKU 需要执行多个步骤并进行多项考虑。 可以遵循以下过程来避免常见问题。
    - **对于非主节点类型：** 建议创建新的虚拟机规模集，修改服务放置约束以包括新的虚拟机规模集/节点类型，然后将旧的虚拟机规模集实例计数降低到零，一次一个节点（这是为了确保删除节点不会影响群集的可靠性）。
    - **对于主节点类型：** 如果所选 VM SKU 达到容量限制，而你希望增大其容量，请按[主节点类型垂直缩放](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type)指南操作。 

- 为任何已启用“黄金”或“白银”耐久性级别的虚拟机规模集保留至少五个节点。
- 持续性级别为“白银”或“黄金”的每个虚拟机规模集，在 Service Fabric 群集中都必须映射到其自己的节点类型。 将多个虚拟机规模集映射到单个节点类型，将阻碍 Service Fabric 群集和 Azure 基础结构间的协调正常工作。
- 不要删除随机 VM 实例，请始终使用虚拟机规模集的功能扩展。 删除随机 VM 实例可能会造成分布在 UD 和 FD 的 VM 实例失衡。 这一失衡可能会对系统在服务实例/服务副本之间进行适当负载均衡的能力产生负面影响。
- 如果使用自动缩放，请设置规则，以便一次只有一个节点执行“缩小规模”操作（删除 VM 实例）。 一次减少多个实例是不安全的。
- 删除或解除分配主节点类型上的 VM 时，决不应当将已分配的 VM 的数量降低到可靠性层需要的数量之下。 在耐久性级别为“白银”或“黄金”的规模集中，这些操作会被无限期阻止。

## <a name="the-reliability-characteristics-of-the-cluster"></a>群集的可靠性特征
可靠性层用于设置想要在此群集中的主节点类型上运行的系统服务副本数。 副本数越大，群集中的系统服务越可靠。  

可靠性层可以采用以下值：

* 白金 - 运行包含 9 个目标副本集的系统服务
* 黄金 - 运行包含 7 个目标副本集的系统服务
* 白银 - 运行包含 5 个目标副本集的系统服务 
* 青铜 - 运行包含 3 个目标副本集的系统服务

> [!NOTE]
> 选择的可靠性层决定了主节点类型必须具有的最少节点数。 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>针对可靠性层的建议

扩大或减小群集规模（所有节点类型中的 VM 实例总和）时，必须将群集的可靠性从一个层更新到另一层。 这样做会触发更改系统服务副本集计数所需的群集升级。 等待升级完成，然后对群集做出其他任何更改，例如添加节点。  可以在 Service Fabric Explorer 中运行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) 来监视升级进度

下面是有关选择可靠性层的建议。  种子节点数也设置为可靠性层的最小节点数。  例如，具有黄金级可靠性的群集有 7 个种子节点。

| **群集节点数** | **可靠性层** |
| --- | --- |
| 1 |不要指定“可靠性层”参数，系统会计算该参数 |
| 3 |Bronze |
| 5 或 6|Silver |
| 7 或 8 |Gold |
| 9 及以上 |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>主节点类型 - 容量指导

下面是用于规划主节点类型容量的指导：

- **要在 Azure 中运行任何生产工作负荷的 VM 实例数：** 必须将最小主节点类型大小指定为 5，将可靠性层指定为“银级”。  
- **要在 Azure 中运行测试工作负荷的 VM 实例数：** 可以将最小主节点类型大小指定为 1 或 3。 单节点群集，采用特殊配置来运行，因此不支持对该群集进行扩展。 单节点群集，不具备可靠性，因此，必须在 Resource Manager 模板中删除/不指定该配置（单纯不设置配置值还不够）。 如果设置通过门户设置的单节点群集，则系统会自动处理该配置。 不支持使用单节点群集和三节点群集运行生产工作负荷。 
- **VM SKU：** 主节点类型是运行系统服务的位置，因此，在为它选择 VM SKU 时，必须考虑到你要在群集中放置的总峰值负载。 下面这个类比用来说明此处我要表达的意思 - 将主节点类型想象成你的“肺”，它向你的大脑提供氧气，如果大脑不能获得足够的氧气，你的身体会感到痛苦。 

由于群集的容量需求是由你计划在群集中运行的工作负荷决定的，因此，我们无法针对具体工作负荷提供定性指导，但是下面的宽泛指导可帮助你入门

对于生产工作负荷： 

- 建议将群集的主要节点类型提供给系统服务，使用位置约束将应用程序部署到辅助节点类型。
- 建议的 VM SKU 为标准 D2_V2 或与之相当的类型，使用至少 50 GB 的本地 SSD。
- 支持使用的最小 VM SKU 为 Standard_D2_V3 或 Standard D1_V2 或与之相当的类型，使用至少 50 GB 的本地 SSD。 
- 建议至少为 50 GB。 对于你的工作负荷，尤其是在运行 Windows 容器时，需要更大的磁盘。 
- 生产工作负荷不支持不完整的核心 VM SKU，例如标准 A0。
- 由于性能原因，不支持将一系列 VM SKU 用于生产工作负荷。
- 不支持低优先级 VM。

> [!WARNING]
> 在正在运行的群集上更改主节点 VM SKU 大小是一种扩展操作，并在[虚拟机规模集横向扩展](virtual-machine-scale-set-scale-node-type-scale-out.md)文档中进行了说明。

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>非主节点类型 - 针对有状态工作负荷的容量指导

本指南适用于使用 Service Fabric [可靠集合或可靠角色](service-fabric-choose-framework.md)（在非主节点类型上运行）的有状态工作负荷。

**VM 实例数：** 对于有状态生产工作负荷，建议使用最小值和目标副本计数 5 运行它们。 这意味着，在稳定的状态下，最终在每个容错域和升级域中都将具有一个副本（来自副本集）。 主节点类型的整体可靠性层概念实际上只是一种为系统服务指定此设置的方法。 因此，这一注意事项同样适用于有状态服务。

因此，对于生产工作负荷，如果在节点中运行有状态工作负荷，建议使用的最小非主节点类型大小为 5。

**VM SKU：** 这是运行应用程序服务的节点类型，因此，为其选择的 VM SKU 必须将你计划在每个节点中放置的峰值负荷考虑在内。 节点类型的容量需求是由你计划在群集中运行的工作负荷决定的，因此，我们无法针对具体工作负荷提供定性指导，但是下面的宽泛指导可帮助你入门

对于生产工作负荷 

- 建议的 VM SKU 为标准 D2_V2 或与之相当的类型，使用至少 50 GB 的本地 SSD。
- 支持使用的最小 VM SKU 为 Standard_D2_V3 或 Standard D1_V2 或与之相当的类型，使用至少 50 GB 的本地 SSD。 
- 部分核心 VM SKU（例如标准 A0）不支持用于生产工作负荷。
- 由于性能原因，不支持将一系列 VM SKU 用于生产工作负荷。

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>非主节点类型 - 针对无状态工作负荷的容量指导

在非主节点类型上运行的无状态工作负荷指南。

**VM 实例数：** 对于无状态生产工作负荷，支持的最小非主节点类型大小为 2。 这允许运行应用程序的两个无状态实例，并允许服务在某个 VM 实例丢失的情况下继续运行。 

**VM SKU：** 这是运行应用程序服务的节点类型，因此，为其选择的 VM SKU 必须将你计划在每个节点中放置的峰值负荷考虑在内。 节点类型的容量需求是由你计划在群集中运行的工作负荷决定的。 我们无法针对你的具体工作负荷提供定性指导。  但是，下面提供了可帮助你入门的宽泛指导。

对于生产工作负荷 

- 建议的 VM SKU 为Standard D2_V2 或相当的容量。 
- 建议使用的 VM SKU 是标准 D1 或标准 D1_V2 或等效项。 
- 部分核心 VM SKU（例如标准 A0）不支持用于生产工作负荷。
- 由于性能原因，不支持将一系列 VM SKU 用于生产工作负荷。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>后续步骤
完成容量规划并设置群集后，请阅读以下文章：

* [Service Fabric 群集安全性](service-fabric-cluster-security.md)
* [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)
* [灾难恢复规划](service-fabric-disaster-recovery.md)
* [Nodetypes 与虚拟机规模集的关系](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
