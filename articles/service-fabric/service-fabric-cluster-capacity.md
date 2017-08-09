---
title: "规划 Service Fabric 群集容量 | Microsoft 文档"
description: "Service Fabric 群集容量规划注意事项。 节点类型、耐久性和可靠性层"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 9d5a4bef0c22f637a35390c6a8a245967fb02118
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Service Fabric 群集容量规划注意事项
对于任何生产部署，容量规划都是一个重要的步骤。 下面是在规划过程中必须注意的一些事项。

* 群集一开始需要的节点类型数目
* 每个节点类型的属性（大小、主节点、面向 Internet、VM 数量等）
* 群集的可靠性和耐久性特征

让我们简单地了解其中的每项。

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>群集一开始需要的节点类型数目
首先，需要确定要创建的群集用于什么目的，以及打算要将哪些类型的应用程序部署到此群集中。 如果不清楚群集的用途，则很可能还未准备好进入容量规划流程。

确定群集一开始需要的节点类型数目。  每个节点类型将映射到虚拟机规模集。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 因此，节点类型数目的确定基本上可归结为以下考虑因素：

* 应用程序是否有多个服务，其中是否有任何服务需是面向公众或面向 Internet 的服务？ 典型的应用程序包含可接收客户端输入的前端网关服务，以及一个或多个与前端服务通信的后端服务。 因此，在此情况下，必须至少有两个节点类型。
* （构成应用程序）的服务是否有不同的基础结构要求，例如，更多的 RAM 或更高的 CPU 周期？ 例如，假设要部署的应用程序包含前端服务和后端服务。 前端服务可以在容量较小（D2 等的 VM 大小）且向 Internet 开放了端口的 VM 上运行。  但是，后端服务计算密集型的服务，需要放在容量较大（D4、D6、D15 等的 VM 大小）且不连接到 Internet 的 VM 上运行。
  
  在本示例中，可以决定将所有服务都放在一个节点类型上，但我们建议将它们分别放在群集中的两个节点类型上。  这样，每个节点类型都有不同的属性，例如，Internet 连接或 VM 大小。 也可以单独缩放 VM 的数目。  
* 由于无法预见未来，因此，请利用所知道的事实，确定应用程序一开始需要的节点类型数目。 以后，随时可以添加或删除节点类型。 Service Fabric 群集必须至少有一个节点类型。

## <a name="the-properties-of-each-node-type"></a>每个节点类型的属性
**节点类型**相当于云服务中的角色。 节点类型定义 VM 大小、VM 数目及其属性。 在 Service Fabric 群集中定义的每个节点类型将设置为不同的虚拟机规模集。 虚拟机规模集是一种 Azure 计算资源，可用于将一组 VM 作为一个集进行部署和管理。 如果定义为不同的虚拟机规模集，则每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。

有关节点类型与虚拟机规模集之间的关系、如何 RDP 到某个实例、打开新端口等方面的更多详细信息，请阅读[此文档](service-fabric-cluster-nodetypes.md)。

群集可以有多个节点类型，但主节点类型（在门户定义的第一个节点类型）必须至少有 5 个 VM 供群集用于生产工作负荷（或至少有 3 个 VM 用于测试群集）。 如果要使用资源管理器模板创建群集，请在节点类型定义下查找“is Primary”属性。 主节点类型是 Service Fabric 系统服务所在的节点类型。  

### <a name="primary-node-type"></a>主节点类型
对于包含多个节点类型的群集，需要选择其中一个作为主节点类型。 下面是主节点类型的特征：

* 主节点类型的 **VM 大小下限**取决于你选择的**耐久性层**。 耐久性层的默认值为“青铜”。 有关耐久性层的定义以及可采用的值的详细信息，请向下滚动。  
* 主节点类型的 **VM 数目下限**取决于选择的**可靠性层**。 可靠性层的默认值为“白银”。 有关可靠性层的定义以及可采用的值的详细信息，请向下滚动。 

 

* Service Fabric 系统服务（例如，群集管理器服务或映像存储服务）放在主节点类型上，因此，群集的可靠性和耐久性取决于你为主节点类型选择的可靠性层值与耐久性层值。

![显示具有两个节点类型的群集的屏幕截图 ][SystemServices]

### <a name="non-primary-node-type"></a>非主节点类型
包含多个节点类型的群集有一个主节点类型，剩余的是非主节点类型。 下面是非主节点类型的特征：

* 此节点类型的 VM 大小下限取决于选择的耐久性层。 耐久性层的默认值为“青铜”。 有关耐久性层的定义以及可采用的值的详细信息，请向下滚动。  
* 此节点类型的 VM 数目下限可以是 1。 但是，应该根据想要在此节点类型中运行的应用程序/服务的副本数目选择此数目。 部署群集之后，节点类型中的 VM 数目可能会增加。

## <a name="the-durability-characteristics-of-the-cluster"></a>群集的耐久性特征
耐久性层用于向系统指示 VM 对于基本 Azure 基础结构拥有的权限。 在主节点类型中，此权限可让 Service Fabric 暂停影响系统服务及有状态服务的仲裁要求的任何 VM 级别基础结构请求（例如，VM 重新启动、VM 重置映像或 VM 迁移）。 在非主节点类型中，此权限可让 Service Fabric 暂停影响其中运行的有状态服务的仲裁要求的任何 VM 级别基础结构请求，例如，VM 重新启动、VM 重置映像、VM 迁移，等等。

此权限表示为以下值：

* 黄金 - 每个 UD 可持续暂停基础结构作业 2 小时。 仅可在 D15_V2、G5 等完整节点 VM sku 上启用“黄金”耐久性。
* 白银 - 可以为每个 UD 持续暂停基础结构作业 10 分钟，并且可在所有单核及多核的标准 VM 上使用。
* 青铜 - 无权限。 这是默认值，建议仅在群集中运行无状态工作负荷时使用。

可以为每个节点类型选择持续性级别。可以为群集中的一个节点类型选择“黄金”持续性级别，为同一群集中的另一个节点类型选择“青铜”持续性级别。对于持续性为“黄金”或“白银”的任何节点类型，必须至少保留 5 个节点。 

**使用“白银”或“黄金”耐久性级别的优点**
 
1. 减少了“缩小规模”操作所需的步骤数（即自动调用节点停用和 Remove-ServiceFabricNodeState）
2. 减少了由于客户启动的就地 VM SKU 更改操作或 Azure 基础结构操作而导致的数据丢失的风险。
     
**使用“白银”或“黄金”耐久性级别的缺点**
 
1. 部署到虚拟机规模集和其他相关 Azure 资源可能会由于群集中的问题或基础结构级别的问题而延迟、超时或被完全阻止。 
2. 由于 Azure 基础结构操作期间发生的自动节点停用而增加了[副本生命周期事件](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle )（例如，主交换）的数量。



### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>有关何时使用“白银”或“黄金”耐久性级别的建议

对于托管要频繁缩小规模（减小 VM 实例计数）的有状态服务并且你愿意延迟部署操作以利于简化这些“缩小规模”操作的所有节点类型，请使用“白银”或“黄金”耐久性。 “扩大规模”的情况（添加 VM 实例）不会受所选耐久性层的影响，只有“缩小规模”的情况才受其影响。

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>适用于已设置为“白银”或“黄金”耐久性级别的节点类型的操作建议。

1. 使群集和应用程序在任何时间都正常工作，并确保应用程序及时响应所有[服务副本生命周期事件](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle)（例如，生成副本时出现停滞）。
2. 采用更安全的方式更改 VM SKU（增加/减少）：更改虚拟机规模集的 VM SKU 本质上是一项不安全的操作，因此如有可能，应尽量采用更安全的方式。 可以遵循以下过程来避免常见问题。
    - 对于非主节点类型：建议创建新的虚拟机规模集，修改服务放置约束以包括新的虚拟机规模集/节点类型，然后将旧的虚拟机规模集实例计数降低到 0，一次一个节点（这是为了确保删除节点不会影响群集的可靠性）。
    - 对于主节点类型：建议不要更改主节点类型的 VM SKU。 如果要使用新 SKU 的原因是提高容量，则建议添加更多实例，或在可能的情况下创建新群集。 如果别无选择，请对虚拟机规模集模型定义进行修改以反映新 SKU。 如果群集只有一个节点类型，请确保所有有状态应用程序及时响应所有[服务副本生命周期事件](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle)（例如，在生成副本时出现停滞），并且重新生成服务副本的持续时间小于 5 分钟（适用于“白银”持续性级别）。 
3. 为已启用 MR 的任何虚拟机规模集至少保留五个节点
4. 不要删除随机 VM 实例，请始终使用虚拟机规模集缩减功能。 删除随机 VM 实例可能会造成分布在 UD 和 FD 的 VM 实例失衡。 这一失衡可能会对系统在服务实例/服务副本之间进行适当负载均衡的能力产生负面影响。
6. 如果使用自动缩放，请设置规则，以便一次只有一个节点执行“缩小规模”操作（删除 VM 实例）。 一次减少多个实例是不安全的。
7. 如果减少某个主节点类型，则绝不应将其缩减到超出可靠性层允许的数目。


## <a name="the-reliability-characteristics-of-the-cluster"></a>群集的可靠性特征
可靠性层用于设置要在此群集中的主节点类型上运行的系统服务副本数。 副本数越大，群集中的系统服务越可靠。  

可靠性层可以采用以下值：

* Platinum - 运行包含 9 个目标副本集的系统服务
* 黄金 - 运行包含 7 个目标副本集的系统服务
* 白银 - 运行包含 5 个目标副本集的系统服务 
* 青铜 - 运行包含 3 个目标副本集的系统服务

> [!NOTE]
> 选择的可靠性层决定了主节点类型必须具有的节点数下限。 
> 
> 


### <a name="recommendations-for-the-reliability-tier"></a>针对可靠性层的建议。

 扩大或减小群集规模（所有节点类型中的 VM 实例总和）时，必须将群集的可靠性从一个层更新到另一层。 这样做会触发更改系统服务副本集计数所需的群集升级。 等待升级完成，并对群集做出其他任何更改，例如添加节点。  可以在 Service Fabric Explorer 中运行 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) 来监视升级进度

下面是有关选择可靠性层的建议。

| 群集大小 | 可靠性层 |
| --- | --- |
| 1 |不要指定“可靠性层”参数，系统会计算该参数 |
| 3 |Bronze |
| 5 或 6|银牌服务 |
| 7 或 8 |金牌服务 |
| 9 及以上 |白金 |




## <a name="primary-node-type---capacity-guidance"></a>主节点类型 - 容量指导

下面是用于规划主节点类型容量的指导

1. 要在 Azure 中运行任何生产工作负荷的 VM 实例数：必须将最小主节点类型大小指定为 5。 
2. 要在 Azure 中运行测试工作负荷的 VM 实例数：可以将最小主节点类型大小指定为 1 或 3。 单节点群集，采用特殊配置来运行，因此不支持对该群集进行扩展。 单节点群集，不具备可靠性，因此，必须在 Resource Manager 模板中删除/不指定该配置（单纯不设置配置值还不够）。 如果设置通过门户设置的单节点群集，则会自动处理该配置。 不支持使用单节点群集和三节点群集运行生产工作负荷。 
3. **VM SKU：**主节点类型是运行系统服务的位置，因此，为其选择的 VM SKU 必须将你计划在群集中放置的总体峰值负荷考虑在内。 下面这个类比用来说明此处我要表达的意思 - 将主节点类型想象成“肺”，它向大脑提供氧气，如果大脑不能获得足够的氧气，身体会痛苦。 

由于群集的容量需求是由你计划在群集中运行的工作负荷决定的，因此，我们无法针对具体工作负荷提供定性指导，但是下面的宽泛指导可帮助你入门

对于生产工作负荷 


- 建议使用的 VM SKU 是标准 D3 或标准 D3_V2 或者最少具有 14 GB 本地 SSD 的等效项。
- 支持使用的最小 VM SKU 是标准 D1 或标准 D1_V2 或者最少具有 14 GB 本地 SSD 的等效项。 
- 部分核心 VM SKU（例如标准 A0）不支持用于生产工作负荷。
- 由于性能原因，不支持将标准 A1 SKU 用于生产工作负荷。


## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>非主节点类型 - 针对有状态工作负荷的容量指导

本指南适用于使用 Service Fabric [可靠集合或可靠角色](service-fabric-choose-framework.md)（在非主节点类型上运行）的有状态工作负荷。


**VM 实例数：**对于有状态生产工作负荷，建议使用最小值和目标副本计数 5 运行它们。 这意味着，在稳定的状态下，最终在每个容错域和升级域中都将具有一个副本（来自副本集）。 主节点类型的整体可靠性层概念实际上只是一种为系统服务指定此设置的方法。 因此，这一注意事项同样适用于有状态服务。

因此，对于生产工作负荷，如果在节点中运行有状态工作负荷，建议使用的最小非主节点类型大小为 5。


**VM SKU：**这是运行应用程序服务的节点类型，因此，为其选择的 VM SKU 必须将你计划在每个节点中放置的峰值负荷考虑在内。 节点类型的容量需求是由你计划在群集中运行的工作负荷决定的，因此，我们无法针对具体工作负荷提供定性指导，但是下面的宽泛指导可帮助你入门

对于生产工作负荷 

- 建议使用的 VM SKU 是标准 D3 或标准 D3_V2 或者最少具有 14 GB 本地 SSD 的等效项。
- 支持使用的最小 VM SKU 是标准 D1 或标准 D1_V2 或者最少具有 14 GB 本地 SSD 的等效项。 
- 部分核心 VM SKU（例如标准 A0）不支持用于生产工作负荷。
- 标准 A1 SKU 由于性能原因尤其不支持用于生产工作负荷。


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>非主节点类型 - 针对无状态工作负荷的容量指导

在非主节点类型上运行的无状态工作负荷指南。

**VM 实例数：**对于无状态生产工作负荷，支持的最小非主节点类型大小为 2。 这允许运行应用程序的两个无状态实例，并允许服务在某个 VM 实例丢失的情况下继续运行。 

> [!NOTE]
> 当群集在低于 5.6 的 Service Fabric 版本上运行时，由于运行时中的一个缺陷（此问题已在 5.6 中修复），如果将非主节点类型缩减为小于 5，则会导致群集运行状况变为不正常，直到使用相应的节点名称调用 [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState)。 有关更多详细信息，请阅读[执行 Service Fabric 群集缩放](service-fabric-cluster-scale-up-down.md)。
> 
>

**VM SKU：**这是运行应用程序服务的节点类型，因此，为其选择的 VM SKU 必须将你计划在每个节点中放置的峰值负荷考虑在内。 节点类型的容量需求是由你计划在群集中运行的工作负荷决定的，因此，我们无法针对具体工作负荷提供定性指导，但是下面的宽泛指导可帮助你入门

对于生产工作负荷 


- 建议使用的 VM SKU 是标准 D3 或标准 D3_V2 或等效项。 
- 建议使用的 VM SKU 是标准 D1 或标准 D1_V2 或等效项。 
- 部分核心 VM SKU（例如标准 A0）不支持用于生产工作负荷。
- 由于性能原因，不支持将标准 A1 SKU 用于生产工作负荷。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>后续步骤
完成容量规划并设置群集后，请阅读以下文章：

* [Service Fabric 群集安全性](service-fabric-cluster-security.md)
* [Service Fabric 运行状况模型简介](service-fabric-health-introduction.md)
* [Nodetype 与虚拟机规模集的关系](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

