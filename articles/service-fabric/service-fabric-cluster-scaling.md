---
title: Azure Service Fabric 群集缩放
description: 了解如何横向或纵向扩展、放大或缩减 Azure Service Fabric 群集。 随着应用程序需求变化，Service Fabric 群集也会发生变化。
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: a21182c974d6141264c8ca0c36bfc8f6a366d6f3
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793170"
---
# <a name="scaling-azure-service-fabric-clusters"></a>缩放 Azure Service Fabric 群集
Service Fabric 群集是通过网络连接在一起的一组虚拟机或物理机，可在其中部署和管理微服务。 属于群集一部分的计算机或 VM 称为节点。 群集可以包含数千个节点。 创建 Service Fabric 群集后，可以群集横向缩放（更改节点数）或纵向缩放（更改节点资源）该群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

为何缩放群集？ 应用程序的需求会不断变化。  可能需要增加群集资源来满足更多的应用程序工作负荷或网络流量，或者在需求下降时减少群集资源。

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>横向扩展和缩减
更改群集中的节点数。  新节点加入群集后，[群集资源管理器](service-fabric-cluster-resource-manager-introduction.md)会将服务移到其中，导致现有节点上的总负载减少。  此外，如果群集的资源未被有效利用，可以减少节点数量。  节点退出群集后，服务会移出这些节点，剩余节点上的负载会增大。  减少 Azure 中运行的群集的节点数可以节省资金，因为我们是根据 VM 的数量付费，而不是根据这些 VM 上的工作负荷付费。  

- 优点：理论上无限缩放。  如果应用程序采用可伸缩性设计，则可以通过添加更多节点来实现无限扩充。  使用云环境中的工具可以轻松添加或删除节点，因此可以方便地调整容量，并且只需为使用的资源付费。  
- 缺点：应用程序必须采用[可伸缩性设计](service-fabric-concepts-scalability.md)。  应用程序数据库和持久性可能需要更多的体系结构工作才能正常缩放。  但是，Service Fabric 有状态服务中的[可靠集合](service-fabric-reliable-services-reliable-collections.md)能够大大简化应用程序数据的缩放。

虚拟机规模集是一种 Azure 计算资源，可用于将一组虚拟机作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后，每个节点类型可以独立缩减或扩展、打开不同的端口集，并可以有不同的容量指标。 

缩放 Azure 群集时，请记住以下准则：
- 运行生产工作负荷的主节点类型应始终具有五个或更多个节点。
- 运行有状态生产工作负荷的非主节点类型应始终具有五个或更多个节点。
- 运行无状态生产工作负荷的非主节点类型应始终具有两个或更多个节点。
- [持久性级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)为金级或银级的任何节点类型应始终具有五个或更多个节点。
- 不要从节点类型中删除随机 VM 实例/节点，始终使用虚拟机规模集的功能范围。 删除随机 VM 实例可能会对系统正确进行负载均衡造成负面影响。
- 如果使用自动缩放规则，请将规则设置为每次对一个节点执行缩减（删除 VM 实例）。 一次减少多个实例是不安全的。

由于群集中的 Service Fabric 节点类型由后端的虚拟机规模集构成，因此可以[设置自动缩放规则，或手动缩放](service-fabric-cluster-scale-in-out.md)每个节点类型/虚拟机规模集。

### <a name="programmatic-scaling"></a>编程缩放
在许多方案中，[手动或使用自动缩放规则缩放群集](service-fabric-cluster-scale-in-out.md)是合理的解决方案。 但是，对于更高级的方案，这种缩放方法可能不合适。 这些方法的潜在缺点包括：

- 手动缩放要求登录并显式请求缩放操作。 如果经常需要执行缩放操作或者执行该操作的时间不可预测，则这种缩放方法可能不是一个很好的解决方案。
- 当自动缩放规则从虚拟机规模集中删除某个实例时，它们不会从关联的 Service Fabric 群集中自动删除该节点的信息，除非节点类型的持久性级别达到了银级或金级。 由于自动缩放规则在规模集级别（而不是 Service Fabric 级别）工作，因此，自动缩放规则可能会在未正常关闭 Service Fabric 节点的情况下将其删除。 在执行缩减操作后，这种强行删除节点的方式会使 Service Fabric 节点保持“虚幻”状态。 个人（或服务）需要定期清理 Service Fabric 群集中已删除节点的状态。
- 持久性级别达到金级或银级的节点类型会自动清理已删除的节点，因此无需任何附加清理。
- 尽管自动缩放规则支持[许多指标](../azure-monitor/platform/autoscale-common-metrics.md)，但指标集的规模仍然有限。 如果方案需要根据该集中未涵盖的某个指标进行缩放，则自动缩放规则可能不是一个适当的选项。

应选择哪种 Service Fabric 缩放方法取决于具体的方案。 如果缩放过程不常见，则具备手动添加或删除节点的能力也许已足够。 在比较复杂的方案中，能够以编程方式缩放的自动缩放规则和 SDK 可用作强大的替代方法。

Azure API 可让应用程序以编程方式使用虚拟机规模集和 Service Fabric 群集。 如果现有的自动缩放选项不适用于方案，可通过这些 API 实现自定义的缩放逻辑。 

实现这种“定制”自动缩放功能的方法之一是，将一个新的无状态服务添加到 Service Fabric 应用程序来管理缩放操作。 创建自己的缩放服务可以针对应用程序的缩放行为实现最大控制度和定制性。 在需要精确何时或者如何缩减或扩展应用程序的方案中，这种方法非常有效。但是，这种控制也附带了代码复杂性方面的弊端。 使用这种方法意味着需要拥有缩放代码，而这并不是一个简单的任务。 在服务的 `RunAsync` 方法中，有一组触发器可以确定是否需要缩放（包括检查最大群集大小等参数，以及缩放减缓）。   

适用于虚拟机规模集交互的 API（用于确定和修改当前虚拟机实例数量）为 [Fluent Azure 管理计算库](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/)。 fluent 计算库提供一个易用的 API 来与虚拟机规模集交互。  若要与 Service Fabric 群集本身交互，可使用 [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient)。

不过，缩放代码无需在群集中以服务的形式运行即可缩放。 `IAzure` 和 `FabricClient` 均可远程连接到其关联的 Azure 资源，因此，缩放服务可以单纯地是一个控制台应用程序，或者是从 Service Fabric 应用程序外部运行的 Windows 服务。

由于这些限制，我们可能想要[实现其他自定义的自动缩放模型](service-fabric-cluster-programmatic-scaling.md)。

## <a name="scaling-up-and-down-or-vertical-scaling"></a>纵向扩展和缩减 
更改群集中节点的资源（CPU、内存或存储）。
- 优点：软件和应用程序体系结构保持不变。
- 缺点：有限缩放，因为在单个节点上增加的资源量有限制。 会造成停机，因为需要使物理机或虚拟机脱机才能添加或删除资源。

虚拟机规模集是一种 Azure 计算资源，可用于将一组虚拟机作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。  纵向扩展或缩减节点类型涉及到更改规模集中虚拟机实例的 SKU。 

> [!WARNING]
> 我们建议不要更改规模集/节点类型的 VM SKU，除非它在[银级持久性或更高的级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)运行。 更改 VM SKU 大小是一种破坏数据的就地基础结构操作。 由于无法延迟或监视此更改，此操作可能会导致有状态服务的数据丢失或其他意外操作问题（甚至可能影响无状态工作负载）。 
>

缩放 Azure 群集时，请记住以下准则：
- 如果减少某个主节点类型，则绝不应将其缩减到超出[可靠性层](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)允许的数目。

根据节点类型是非主节点类型还是主节点类型，其纵向缩放过程有所不同。

### <a name="scaling-non-primary-node-types"></a>缩放非主节点类型
使用所需的资源创建新节点类型。  更新运行中服务的位置约束，以包含新节点类型。  将旧节点类型的实例计数逐渐（一次一个）减少至零，以免影响群集的可靠性。  在解除旧节点类型授权的过程中，服务会逐渐迁移到新节点类型。

### <a name="scaling-the-primary-node-type"></a>缩放主节点类型
我们建议不要更改主节点类型的 VM SKU。 如果需要更多群集容量，我们建议添加更多实例。 

如果那不可行，可以创建新群集并从旧群集[还原应用程序状态](service-fabric-reliable-services-backup-restore.md)（如果适用）。 不需要还原任何系统服务状态，在将应用程序部署到新群集时就已重新创建它们。 如果只在群集上运行无状态应用程序，则只需将应用程序部署到新群集即可，无需还原任何内容。 如果一定要进行不受支持的操作，更改 VM SKU，请修改虚拟机规模集模型定义以反映新的 SKU。 如果群集只有一个节点类型，请确保所有有状态应用程序及时响应所有[服务副本生命周期事件](service-fabric-reliable-services-lifecycle.md)（例如，在生成副本时出现停滞），并且重新生成服务副本的持续时间小于五分钟（适用于“银级”持久性级别）。 

## <a name="next-steps"></a>后续步骤
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

