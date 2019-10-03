---
title: 描述群集使用群集资源管理器 |Microsoft Docs
description: 通过指定容错域、 升级域、 节点属性和节点容量的群集资源管理器来描述 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271642"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>通过使用群集资源管理器来描述 Service Fabric 群集
Azure Service Fabric 群集资源管理器功能提供了多种用于描述群集的机制：

* 容错域
* 升级域
* 节点属性
* 节点容量

在运行时，群集资源管理器使用此信息以确保在群集中运行的服务的高可用性。 实施这些重要规则，同时它也尝试优化群集内的资源消耗。

## <a name="fault-domains"></a>容错域
容错域是协调故障的任何区域。 一台计算机是一个容错域。 它可能在其自己出于各种原因，从电源故障 NIC 固件错误，等等的驱动器故障发生故障。 

连接到同一以太网交换机的计算机位于同一个容错域。 所以都共享单个源的能力或在单个位置的计算机。 

因为它是重叠的硬件故障，容错域是本质上是分层的。 它们被表示为 Service Fabric 中的 Uri。

非常重要，容错域正确设置了因为 Service Fabric 使用此信息来安全放置服务。 Service Fabric 不希望丢失的容错域 （由某个组件故障而导致） 会造成服务停机放置服务。 

在 Azure 环境中，Service Fabric 使用环境提供的容错域信息来正确配置你的名义群集中的节点。 对于独立的 Service Fabric 实例，在设置群集时定义容错域。 

> [!WARNING]
> 务必提供给 Service Fabric 的容错域信息准确无误。 例如，假设，5 个物理主机上运行的 10 个虚拟机内部运行 Service Fabric 群集的节点。 在此情况下，即使有 10 个虚拟机，也只有 5 个不同的（顶层）容错域。 共享同一个物理主机会导致 Vm 共享同一个根容错域，因为如果其物理主机发生故障，Vm 也会协调的故障。  
>
> Service Fabric 预期节点不能更改的容错域。 确保高可用性的 Vm，如其他机制[HA Vm](https://technet.microsoft.com/library/cc967323.aspx)，可能会导致与 Service Fabric 产生冲突。 这些机制使用到另一台主机的 Vm 透明迁移。 它们并不重新配置或通知 VM 内运行的代码。 在这种情况下，它们*不支持*作为环境运行 Service Fabric 群集。 
>
> 应使用 Service Fabric 作为唯一的高可用性技术。 实时 VM 迁移和 San 等机制并不必要。 如果这些机制使用与 Service Fabric 结合使用它们_减少_应用程序可用性和可靠性。 原因是它们引入其他复杂性、 加大故障，和与 Service Fabric 中的使用冲突的可靠性和可用性策略。 
>
>

在下图中，我们的颜色导致容错域和列出导致的所有不同的容错域的所有实体。 本示例列出了数据中心 (DC)、机架 (R) 和刀片服务器 (B)。 如果每个边栏选项卡包含多个虚拟机，容错域层次结构中可能有另一个层。

<center>

![通过容错域组织的节点][Image1]
</center>

在运行时，Service Fabric 群集 Resource Manager 会考虑群集中的容错域，并规划布局。 因此它们在单独的容错域中分发的有状态副本或无状态服务实例。 跨容错域分发服务可确保当层次结构的任一级别发生故障的容错域服务的可用性不受侵害。

群集资源管理器不会考虑容错域层次结构中有多少层。 它会尝试确保丢失任何一部分层次结构不会影响其中运行的服务。 

最好相同的节点数是每个级别的容错域层次结构中的深度。 如果在群集中，容错域"树"不均衡，更难是为群集资源管理器来找出最佳的服务分配。 不均衡的容错域布局意味着某些域的丢失将影响多个其他域的服务的可用性。 因此，群集资源管理器是数据被破坏两个目标之间： 

* 它希望通过将服务放置在该"繁重"域中使用计算机。 
* 要将服务放置在其他域中，以便避免域丢失造成问题。 

失衡的域是什么样子？ 下图显示了两个不同的群集布局。 在第一个示例中，节点均匀分散到容错域。 在第二个示例中，一个容错域具有很多更多节点比其他容错域。 

<center>

![两种不同的群集布局][Image2]
</center>

在 Azure 中，为你托管了的选择哪个容错域包含一个节点。 但具体取决于预配的节点数，您仍然可能出现与在其他用例中它们比有多个节点的容错域。 

例如，假设你有在群集中的 5 个容错域，但预配了七个节点的节点类型 (**NodeType**)。 在这种情况下，第一批的两个容错域最终会得到更多的节点。 如果你继续部署的详细信息**NodeType**实例只需单击几次的情况下，问题会变得更糟。 出于此原因，我们建议每个节点类型中的节点数是容错域数的倍数。

## <a name="upgrade-domains"></a>升级域
升级域是另一项功能，可帮助 Service Fabric 群集 Resource Manager 了解群集的布局。 升级域定义同时升级的节点集。 升级域可帮助了解和安排诸如升级等管理操作的群集资源管理器。

升级域非常类似于容错域，但有几个关键差异。 首先，协调的硬件故障区域定义容错域。 但是，升级域由策略定义。 您可以决定所需的数量，而不是让规定数量的环境。 可以有任意多个升级域节点一样。 容错域和升级域的另一个区别是，升级域不是层次结构。 相反，它们更像是简单的标记。 

下图显示了在三个容错域之间条带化的三个升级域。 它还显示了一个可能的放置三个不同副本的有状态服务，其中每个最终在不同的容错和升级域。 这种放置允许在服务升级容错域的丢失，并且仍有代码和数据的一个副本。  

<center>

![放置与容错域和升级域][Image3]
</center>

有优点也有缺点大量升级域。 更多升级域意味着升级的每个步骤更细微，影响较小数目的节点或服务。 较少的服务必须一次移动较少改动引入系统。 这往往会提高可靠性，因为该服务的更少受升级过程中发生的任何问题。 更多升级域也意味着您需要以处理升级影响的其他节点上的可用性降低缓冲区。 

例如，如果有五个升级域，在每个节点处理大约 20%的流量。 如果需要记下该升级域进行升级，该负载通常需要转到某个位置。 由于你有四个剩余升级域，每个必须具有大约 5%的总流量的空间。 更多升级域意味着您需要在群集中节点上的缓冲区更少。 

请考虑是否改为有 10 个升级域。 在这种情况下，每个升级域处理只有大约 10%的总流量。 当通过群集升级步骤时，每个域将需要留出空间仅有约 1.1%的总流量。 更多升级域通常能够以更高的利用率运行节点因为需要较低保留的容量。 这同样适用于容错域。  

多个升级域的缺点是，升级往往会花费更长时间。 Service Fabric 会等待片刻后升级域完成并执行开始升级下一个之前的检查。 通过这一延迟时间，可在继续升级之前检测升级带来的问题。 这种折衷是可接受的，因为可以防止错误的更改一次性影响过多的服务。

存在升级域太少会产生许多负面影响。 虽然每个升级域并正在升级时，大部分的整体容量将不可用。 例如，如果必须只有三个升级域，则会采用下大约三分之一的整体服务或群集容量一次。 在一次向下让如此多的服务并非理想的结果，因为需要在其余的群集中有足够容量来处理工作负荷。 维护该缓冲区意味着在正常操作期间，这些节点是不是其他的负载更少。 这会增加运行服务的成本。

环境中容错或升级域的总数没有实际限制，对于它们如何重叠也没有约束。 但是，常见模式：

- 容错域和升级域按 1:1 的映射
- 每个节点 （物理或虚拟 OS 实例） 的一个升级域
- "条带化"或"矩阵"模型，其中的容错域和升级域构成具有沿对角线分布的计算机的矩阵

<center>

![容错域和升级域的布局][Image4]
</center>

要选择的布局没有最佳答案。 每个选项都有各自的利与弊。 例如，1FD:1UD 模型很容易设置。 每个节点模型的一个升级域的模型是最常见的类似习惯于。 在升级期间，将单独更新每个节点。 这类似于过去手动升级少量计算机的方式。

最常见的模型是 FD/UD 矩阵，其中的容错域和升级域构成一个表，节点沿着对角线开始放置。 这是 Azure 中 Service Fabric 群集默认使用的模型。 对于具有多个节点的群集，所有布局最终密集矩阵模式类似。

> [!NOTE]
> 在 Azure 中托管的 Service Fabric 群集不支持更改默认策略。 仅独立群集提供该自定义项。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容错域与升级域约束及最终行为
### <a name="default-approach"></a>默认方法
默认情况下，群集资源管理器会保持平衡跨容错域和升级域的服务。 这将建模为[约束](service-fabric-cluster-resource-manager-management-integration.md)。 容错域和升级域状态的约束："对于给定的服务分区，应该永远不会有差异大于 1 的相同级别的层次结构上的任何两个域之间的服务对象 （无状态服务实例或有状态服务副本） 数。"

让我们假设此约束，提供了"最大差值"保证。 容错域和升级域约束会阻止某些移动或排列方式与该规则冲突。

例如，假设我们有一个群集包含六个节点，配置了 5 个容错域和五个升级域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

现在假设我们创建与服务**TargetReplicaSetSize** (或无状态服务**InstanceCount**) 的五个值。 副本驻留在 N1-N5 上。 事实上，无论创建多少个此类服务，都不会用到 N6。 为什么？ 让我们看看 N6 与当前布局之间的差异，以及选择 N6 时会发生情况。

下面是获得的布局和每个容错域和升级域的副本总数：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |第 |
| **UD1** | |R2 | | | |第 |
| **UD2** | | |R3 | | |第 |
| **UD3** | | | |R4 | |第 |
| **UD4** | | | | |R5 |第 |
| **FDTotal** |第 |1 |1 |1 |第 |- |

此布局被均衡的每个容错域和升级域节点。 它也被均衡的每个容错域和升级域的副本数。 每个域都拥有相同数量的节点，以及相同数量的副本。

现在，让我们看看如果使用 N6 而不是 N2，会发生什么情况。 副本将如何分布？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |第 |
| **UD1** |R5 | | | | |第 |
| **UD2** | | |R2 | | |第 |
| **UD3** | | | |R3 | |第 |
| **UD4** | | | | |R4 |第 |
| **FDTotal** |2 |0 |1 |1 |第 |- |

此布局违反了容错域约束的"最大差值"保证的定义。 FD0 有两个副本，而 FD1 有 0 个。 FD0 和 FD1 之间的区别是总共两个，即大于最大差值 1。 由于违反了约束，群集资源管理器不允许这种排列方式。 同样，如果选择 N2 和 N6 （而不是 N1 和 N2），我们会得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |第 |
| **UD3** | | | |R3 | |第 |
| **UD4** | | | | |R4 |第 |
| **FDTotal** |第 |1 |1 |1 |第 |- |

此布局被均衡的容错域。 但现在它违反了升级域约束，因为 UD0 有零个副本，并且 UD1 有两个。 此布局也是无效，不会选取由群集资源管理器。

这种有状态副本或无状态实例的分布方法提供了最佳容错能力。 如果一个域出现故障，会丢失最少数量的副本/实例。 

另一方面，此方法过于严格，不允许群集使用所有资源。 对于某些群集配置，某些节点不可用。 这会导致 Service Fabric 无法放置服务，从而出现警告消息。 在上一示例中，某些群集节点不能为使用 (在示例中的 N6)。 即使节点添加到该群集 (N7 N10) 中时，副本/实例会放置仅在 N1-N5 上由于容错域和升级域的约束。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>另一种方法

群集资源管理器支持容错域和升级域约束的另一个版本。 它允许同时仍然保证最低级别的安全的位置。 可以按如下所示指定备用约束："对于给定的服务分区，域的副本分布应确保分区不会遭受仲裁丢失。" 比方说，此约束保证"仲裁安全"。 

> [!NOTE]
> 对于有状态服务，如果大部分分区副本同时关闭，则表示“仲裁丢失”。 例如，如果**TargetReplicaSetSize**为 5，所有三个副本的一组表示仲裁。 同样，如果**TargetReplicaSetSize**是六个、 四个副本所需的仲裁。 在这两种情况下，不能超过两个副本可能会关闭在同一时间如果分区想要继续正常发挥作用。 
>
> 对于无状态服务，则就没有*仲裁丢失*。 无状态服务仍然可以正常运行，即使是大部分实例在同一时间停机。 因此，我们将重点介绍在本文的其余部分中的有状态服务。
>

返回上一个示例。 使用"仲裁安全"版本，所有三个布局都是约束的有效的。 即使第二个布局中的失败，FD0 或第三个布局中的 UD1 失败，该分区仍有仲裁。 （大部分副本仍可运行。）与此版本的约束，几乎始终可以使用 N6。

"仲裁安全"方法提供了比"最大差值"方法更大的灵活性。 原因是更轻松地找到在几乎所有群集拓扑中的有效副本分布。 但是，此方法不能保证最佳容错特性，因为有些故障比其他故障更为严重。 

在最坏的情况下，大部分副本可能会丢失与一个域和一个其他副本的故障。 例如，而不是三个要求都将失去仲裁具有五个副本或实例的故障，你可以现在丢失大部分副本仅 2 个故障。 

### <a name="adaptive-approach"></a>自适应方法
由于这两种方法具有的优势和劣势，我们引入了将合并这两种策略的自适应方法。

> [!NOTE]
> 这是从 Service Fabric 版本 6.2 开始的默认行为。 
> 
> 自适应方法默认使用“最大差值”逻辑，并且仅在必要时切换为“仲裁安全”逻辑。 群集 Resource Manager 自动找出哪种策略是有必要看一下如何配置的群集和服务。
> 
> 有关这些条件的服务这两个条件成立，群集资源管理器应使用"基于仲裁"逻辑：
>
> * **TargetReplicaSetSize**服务是由容错域数和升级域数整除。
> * 节点数小于或等于数的容错域与升级域数的乘积。
>
> 切记，群集资源管理器将使用这种方法，对于无状态和有状态服务，但没有适用于无状态服务的仲裁丢失。

让我们回到前面的示例，假设群集现在有八个节点。 群集仍然配置了五个容错域和五个升级域，并**TargetReplicaSetSize**该群集上托管的服务的值仍为 5。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

因为所有必要条件，群集资源管理器将使用在分布服务的"基于仲裁"逻辑。 这样，使用 N6-N8。 一个可能的服务分布在这种情况下可能类似以下形式：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |第 |
| **UD1** |R2 | | | | |第 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |第 |
| **FDTotal** |2 |第 |第 |0 |第 |- |

如果你的服务**TargetReplicaSetSize**值减到 4 （举例而言） 后，群集资源管理器将发现所做的更改。 它将继续使用"最大差值"逻辑，因为**TargetReplicaSetSize**不再不再能被故障域和升级域数。 因此，某些副本移动会分发节点 N1-N5 上剩余的四个副本。 这样一来，才不会违反容错域和升级域逻辑的"最大差值"版本。 

在以前的布局中，如果**TargetReplicaSetSize**值为 5 和从群集中删除 N1、 升级域数变为 4。 同样，群集资源管理器开始使用"最大差值"逻辑，因为升级域的数目不均匀地分配的服务**TargetReplicaSetSize**不再值。 因此，副本 R1，同样，生成时具有登陆必须位于 N4 上，这样才不会违反容错域和升级域的约束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |不适用 |不适用 |不适用 |不适用 |不适用 |不适用 |
| **UD1** |R2 | | | | |第 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |第 |
| **UD4** | | | | |R5 |第 |
| **FDTotal** |第 |1 |1 |1 |第 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>配置容错域和升级域
在 Azure 托管的 Service Fabric 部署中，容错域和升级域是自动定义。 Service Fabric 从 Azure 中选择并使用环境信息。

如果你要创建你自己的群集 （或想要在开发过程中运行特定的拓扑），可以提供容错域和升级域信息自己。 在此示例中，我们定义跨三个数据中心 （各有 3 个机架） 的 9 节点本地开发群集。 此群集还包含三个升级域跨这三个数据中心条带化。 下面是 ClusterManifest.xml 中配置的一个示例：

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

此示例使用 ClusterConfig.json 进行独立部署：

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> 在定义群集通过 Azure 资源管理器中时, Azure 分配容错域和升级域。 因此，节点类型和虚拟机规模的定义设置 Azure 资源管理器模板不包括有关容错域或升级域的信息。
>

## <a name="node-properties-and-placement-constraints"></a>节点属性和放置约束
有时 （事实上，大多数情况下） 将想要确保仅在某些类型的群集中的节点上运行某些工作负荷。 例如，某些工作负荷可能需要 Gpu 或 Ssd，而其他人可能不会。 

将特定硬件用于特定的工作负荷的一个极好示例是几乎每个 n 层体系结构。 某些计算机充当前端或应用程序的 API 服务端，并向客户端或 internet 公开。 其他一些计算机（通常具有不同的硬件资源）处理计算或存储层的工作。 通常不会直接向客户端或 Internet 公开这些计算机。 

Service Fabric 预期，在某些情况下，特定的工作负荷可能需要在特定硬件配置上运行。 例如：

* 现有的 n 层应用程序已"提升并迁移"到 Service Fabric 环境。
* 必须在出于性能、 规模或安全性隔离原因的特定硬件上运行工作负荷。
* 工作负荷应与其他工作负荷出于策略或资源消耗原因隔离。

若要支持这种配置，Service Fabric 包括将应用于节点的标记。 这些标记称为节点属性。 *放置约束*是附加到一个或多个节点属性选择的单个服务的语句。 放置约束定义服务运行的位置。 约束集是可扩展的。 可以处理任何键/值对。 

<center>

![不同的工作负荷的群集布局][Image5]
</center>

### <a name="built-in-node-properties"></a>内置节点属性
Service Fabric 定义这样就不必定义它们可以自动使用一些默认节点属性。 在每个节点上定义的默认属性是**NodeType**并**NodeName**。 

例如，可以编写将放置约束作为`"(NodeType == NodeType03)"`。 **NodeType**是常用的属性。 它很有用，因为它与计算机的类型对应 1:1。 每种计算机类型都与一种传统 n 层应用程序的工作负荷类型相对应。

<center>

![放置约束和节点属性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置约束和节点属性语法 
节点属性中指定的值可以是字符串、 布尔值，或带符号的长时间。 在服务上的语句称为放置*约束*因为它可以约束服务可以在群集中运行的位置。 约束可以是任何群集中的节点属性进行操作的布尔值语句。 这些布尔值语句中的有效选择器是：

* 用于创建特定语句的条件检查：

  | 声明专用纸 | 语法 |
  | --- |:---:|
  | “等于” | "==" |
  | “不等于” | "!=" |
  | “大于” | ">" |
  | “大于等于” | ">=" |
  | “小于” | "<" |
  | “小于等于” | "<=" |

* 用于分组和逻辑运算的布尔值语句：

  | 声明专用纸 | 语法 |
  | --- |:---:|
  | “和” | "&&" |
  | “或” | "&#124;&#124;" |
  | “非” | "!" |
  | “组合成单个语句” | "()" |

下面是基本约束语句的一些示例：

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

只有整个放置约束语句求值为“True”的节点才能放置服务。 没有定义属性的节点不匹配包含该属性的任何放置约束。

让我们假设以下节点属性为 ClusterManifest.xml 中的节点类型定义：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

下面的示例演示通过 ClusterConfig.json 定义的独立部署或将 Template.json 用于 Azure 托管群集的节点属性。 

> [!NOTE]
> 在 Azure 资源管理器模板中的节点类型通常已参数化。 它将如下所示`"[parameters('vmNodeType1Name')]"`而不是 NodeType01。
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

您可以创建服务放置*约束*服务，如下所示：

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

如果确定 NodeType01 的所有节点都都有效，还可以选择该节点类型，并且约束`"(NodeType == NodeType01)"`。

可以在运行时动态更新服务的放置约束。 如果需要可以在群集中移动服务、 添加和删除要求，以及其他操作。 Service Fabric 可确保进行这些类型的更改时，即使该服务可保持运行且可用。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置约束是针对每个命名的服务实例指定的。 更新始终会取代（覆盖）以前指定的值。

群集定义用于定义节点上的属性。 更改节点的属性，需要向群集配置升级。 升级节点属性需要重启每个受影响的节点以报告其新属性。 Service Fabric 管理这些滚动升级。

## <a name="describing-and-managing-cluster-resources"></a>描述和管理群集资源
任何协调器的最重要作业之一是帮助管理群集中的资源消耗。 而管理群集资源时需要注意的事项有所不同。 

首先，确保计算机不会过载。 这是指确保计算机运行的服务数不超过其可处理的服务数。 

其次，还有权衡和优化，这对有效运行服务至关重要。 具成本效益或性能敏感型服务产品不允许某些节点处于热状态，而有些则是冷。 热节点会导致资源争用和性能不佳。 冷的节点表示资源浪费和成本增加。 

Service Fabric 代表资源作为*指标*。 指标是你想要向 Service Fabric 描述的任何逻辑或物理资源。 指标的示例是"WorkQueueDepth"或"MemoryInMb"。 有关 Service Fabric 可以管理的物理资源节点上的信息，请参阅[资源调控](service-fabric-resource-governance.md)。 有关配置自定义指标及其用法的信息，请参阅[这篇文章](service-fabric-cluster-resource-manager-metrics.md)。

指标与放置约束和节点属性不同。 节点属性是节点自身的静态描述符。 指标描述节点具有的资源和服务使用的节点上运行时。 节点属性可能会**HasSSD**和可能设置为 true 或 false。 在 SSD 和服务消耗多少可用空间量是类似于"DriveSpaceInMb。"的指标 

放置约束和节点属性一样 Service Fabric 群集 Resource Manager 不理解度量值平均值的名称。 指标名称只是一些字符串。 最好将单位声明为创建时可能会不明确的指标名称的一部分。

## <a name="capacity"></a>容量
如果关闭所有资源*平衡*，Service Fabric 群集资源管理器仍会确保没有节点超过其容量。 对容量溢出进行管理是可能的，除非群集过于饱和或工作负荷大于任何节点。 容量是另一种*约束*群集 Resource Manager 用来了解包含的节点的资源量。 系统还会跟踪整个群集的剩余容量。 

服务级别的容量和消耗量均以指标来表示。 例如，指标可能是"ClientConnections"，节点可能拥有 32768"ClientConnections"单位的容量。 其他节点可能有其他限制。 该节点上运行的服务可以说它目前正在消耗 32,256 指标"ClientConnections"。

在运行时，群集资源管理器跟踪群集中和在节点上剩余容量。 若要跟踪的容量，群集资源管理器中减去从运行该服务的节点的容量的每个服务的使用情况。 使用此信息，群集 Resource Manager 可找出要放置或移动副本，使节点不会超出容量。

<center>

![群集节点和容量][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

您可以看到在群集清单中定义的容量。 下面是一个示例，用于 ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

下面是通过 ClusterConfig.json 定义为独立部署或将 Template.json 用于 Azure 托管群集的容量的示例： 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

服务的动态负载经常发生更改。 假设副本的负载的"ClientConnections"更改从 1,024 为 2048。 然后在正在运行的节点必须唯一 512 该指标的剩余容量。 现在该副本或实例的位置无效，因为该节点上没有足够的空间。 群集资源管理器必须获取返回低于容量的节点。 它减少了通过一个或多个副本或实例从该节点移动到其他节点超出容量的节点上的负载。 

群集资源管理器尝试将副本移动的成本降至最低。 可以深入了解[移动成本](service-fabric-cluster-resource-manager-movement-cost.md)而是有关[重新均衡策略和规则](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="cluster-capacity"></a>群集容量
Service Fabric 群集 Resource Manager 如何保持整个群集过于饱和？ 使用动态负载时，是不可以执行很多。 服务可以有自己的负载高峰独立于群集资源管理器所执行的操作。 因此，拥有足够的今天群集可能是如果明天在高峰期，但功能。 

控制群集资源管理器中可以帮助防止出现问题。 可以执行的第一个操作是阻止创建新的工作负荷会导致群集空间变满。

假设您创建一个无状态服务，并且它具有某些与之关联的负载。 该服务关注"DiskSpaceInMb"指标。 该服务将使用 5 个单位的"DiskSpaceInMb"服务的每个实例。 我们需要创建 3 个服务实例。 这意味着需要 15 个单位的"DiskSpaceInMb"必须位于您甚至可以创建这些服务实例的群集。

群集资源管理器会持续计算容量和每个指标的消耗量，因此它可以确定群集中的剩余容量。 如果没有足够的空间，群集资源管理器会拒绝创建服务调用。

由于要求仅是 15 个单位的将可用，你可以以多种不同方式分配此空间。 例如，可能有一个剩余单位的 15 个不同节点上的容量或三个剩余单位的五个不同节点上的容量。 如果群集资源管理器可以重新排列五个单位上有三个节点，则可放置服务。 重新排列群集通常是可行的，除非群集几乎已满，或者出于某种原因无法合并现有服务。

## <a name="buffered-capacity"></a>缓冲的容量
缓冲的容量是群集资源管理器的另一项功能。 使用此功能可以预留总节点容量的一部分。 此容量缓冲区仅用于在升级和节点失败期间放置服务。 

每个指标的所有节点全局指定缓冲的容量。 为保留容量选择的值是在群集中具有的容错域和升级域数的函数。 更多的容错域和升级域意味着可以选择为缓冲处理容量较小的数字。 如果域数较多，在升级期间和发生故障时不可用的群集数很可能较少。 指定缓冲的容量是有意义，仅当也指定了指标的节点容量。

下面是如何指定缓冲的容量 ClusterManifest.xml 中的示例：

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

下面是如何指定缓冲的容量通过 ClusterConfig.json 的独立部署或将 Template.json 用于 Azure 托管群集的示例：

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

创建新服务会在群集耗尽指标的缓冲容量时失败。 通过防止创建新服务来保留缓冲区，可确保升级和故障不会造成节点超出容量。 缓冲的容量是可选的但我们建议将其在任何群集中定义的指标容量。

群集资源管理器公开此负载信息。 对于每个指标，此信息包括： 
- 缓冲的容量设置。
- 总容量。
- 当前的消耗量。
- 每个度量值是否被视为均衡。
- 有关标准偏差统计信息。
- 最大的节点和最小负载。  
  
下面的代码演示了该输出的示例：

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>后续步骤
* 群集资源管理器中的体系结构和信息流的信息，请参阅[群集资源管理器体系结构概述](service-fabric-cluster-resource-manager-architecture.md)。
* 定义碎片整理指标是合并而不是分散的节点上负载的一种方法。若要了解如何配置重整，请参阅[的 Service Fabric 中指标和负载的碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 从头开始并[了解到 Service Fabric 群集 Resource Manager](service-fabric-cluster-resource-manager-introduction.md)。
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请参阅[均衡 Service Fabric 群集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
