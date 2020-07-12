---
title: 使用群集资源管理器描述群集
description: 通过为群集资源管理器指定容错域、升级域、节点属性和节点容量来描述 Service Fabric 群集。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f473b70d260c552dc67d00715b6ee4bc56b670e0
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246548"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>使用群集资源管理器描述 Service Fabric 群集
Azure Service Fabric 的群集资源管理器功能提供多种机制用于描述群集：

* 容错域
* 升级域
* 节点属性
* 节点容量

在运行时，群集资源管理器使用此信息来确保群集中运行的服务的高可用性。 实施这些重要规则时，群集资源管理器还会尝试优化群集中的资源消耗。

## <a name="fault-domains"></a>容错域
容错域是协调故障的任何区域。 一台计算机就是一个容错域。 该计算机本身可能出于各种原因而发生故障，包括电源故障、驱动器故障、NIC 固件错误，等等。 

连接到同一以太网交换机的计算机位于同一个容错域中。 共享一个电源或者位于同一个位置的计算机或也是如此。 

由于硬件故障在性质上是重叠的，容错域原本就有层次性。 容错域在 Service Fabric 中以 URI 的形式表示。

对容错域进行正确设置很重要，因为 Service Fabric 根据此信息来安全放置服务。 Service Fabric 不希望在放置服务后，容错域的丢失（由某个组件的故障而导致）会造成服务关闭。 

在 Azure 环境中，Service Fabric 利用环境提供的容错域信息，代表用户正确配置群集中的节点。 对于独立的 Service Fabric 实例，将在设置群集时定义容错域。 

> [!WARNING]
> 请务必确保提供给 Service Fabric 的容错域信息准确无误。 例如，假设 Service Fabric 群集的节点在 5 个物理主机上运行的 10 台虚拟机中运行。 在此情况下，即使有 10 个虚拟机，也只有 5 个不同的（顶层）容错域。 共享同一物理主机会导致 VM 共享同一个根容错域，因此如果物理主机发生故障，共享主机的 VM 也会同时相应发生故障。  
>
> Service Fabric 预期节点的容错域不变。 确保实现 VM 高可用性的其他机制（例如 [HA-VM](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10))）可能会导致与 Service Fabric 发生冲突。 这些机制使用主机间的 VM 透明迁移。 它们不会重新配置或通知 VM 内运行的代码。 因此，不支持将这些机制用作运行 Service Fabric 群集的环境。  
>
> 应使用 Service Fabric 作为唯一的高可用性技术。 实时 VM 迁移和 SAN 等机制不是必要的机制。 如果将这些机制与 Service Fabric 一同使用，会降低应用程序的可用性和可靠性。  原因是这些机制会增大复杂性，增加并发性故障来源，并且它们使用的可靠性和可用性策略可能会与 Service Fabric 中的策略相冲突。 
>
>

在下图中，我们已将构成容错域的所有实体着色，并列出了生成的所有不同容错域。 本示例列出了数据中心 (DC)、机架 (R) 和刀片服务器 (B)。 如果每个刀片服务器包含多个虚拟机，则容错域层次结构中可能有另一个层。

<center>

![通过容错域组织的节点][Image1]
</center>

在运行时，Service Fabric 群集资源管理器会考虑群集中的容错域，并规划布局。 它会分发服务的有状态副本和无状态副本，使这些副本位于不同的容错域中。 容错域在层次结构的任一级别发生故障时，通过跨容错域分发服务可确保服务可用性不受影响。

群集资源管理器不在意容错域层次结构中有多少层。 它尝试确保失去层次结构的任何一个部分不会影响在其中运行的服务。 

最好是在容错域层次结构中的每个深度级别部署相同数目的节点。 如果群集中的容错域的 "树" 不均衡，则群集资源管理器更难确定最佳的服务分配。 容错域布局失衡意味着，丢失某些域对服务可用性造成的影响比丢失其他域更严重。 因此，群集资源管理器难以在两个目标之间作出取舍： 

* 它需要通过将服务放置在该 "繁重" 域中来使用这些计算机。 
* 它想要将服务放在其他域中，以便域丢失不会造成问题。 

失衡的域是什么样子？ 下图显示了两种不同的群集布局。 在第一个示例中，节点均匀分散到容错域。 在第二个示例中，某一容错域包含的节点比其他容错域多出许多。 

<center>

![两种不同的群集布局][Image2]
</center>

在 Azure 中，系统会为用户选择哪个容错域包含节点。 但是，根据预配的节点数目，仍然可能出现某容错域比其他容错域承载更多节点的情况。 

例如，假设群集中有 5 个容错域，但针对某个节点类型 (**NodeType**) 预配了 7 个节点。 在这种情况下，前两个容错域会承载更多的节点。 如果继续使用几个实例部署更多的 **NodeType** 实例，问题会变得更严重。 因此，我们建议将每个节点类型中的节点数配置为容错域数的倍数。

## <a name="upgrade-domains"></a>升级域
升级域是另一项功能，可帮助 Service Fabric 群集资源管理器了解群集的布局。 升级域定义同时升级的节点集。 升级域可帮助群集资源管理器了解和安排诸如升级等管理操作。

升级域非常类似于容错域，但有几个重要的差异。 首先，容错域由协调性硬件故障区域定义。 而升级域由策略定义。 需要确定所需的数量，而不是由环境描述所需的数量。 升级域数量可以与节点数量相同。 容错域和升级域之间的另一个差异在于，升级域不是分层的。 升级域更像是一个简单标记。 

下图显示了跨三个容错域条带化的三个升级域。 该图还显示了有状态服务的三个不同副本的一种可能的放置方式，即三个副本位于不同的容错域和升级域中。 这种放置方式容许在服务升级过程中丢失一个容错域，在这种情况下仍可运行一个代码和数据副本。  

<center>

![容错域和升级域的放置][Image3]
</center>

有大量升级域既有利也有弊。 更多升级域意味着升级的每个步骤都更精细，影响的节点或服务数更少。 每次移动的服务越少，给系统带来的流失就越少。 这往往会提高可靠性，因为升级期间引入的任何问题对服务的影响更小。 更多升级域也意味着在其他节点上需要更少的可用缓冲区来处理升级的影响。 

例如，如果有五个升级域，每个域中的节点处理大约 20% 的流量。 如果需要关闭升级域进行升级，则通常需要将负载转移到某个位置。 由于你有四个剩余升级域，因此每个域的总流量必须为大约25%。 更多升级域意味着群集中节点上所需占用的缓冲区更少。

假设你有 10 个升级域。 在此情况下，每个升级域仅处理大约 10% 的总流量。 当升级步骤通过群集时，每个域只需有大约11% 的总流量。 使用更多升级域通常能够以更高的利用率运行节点，因为需要保留的容量更少。 这同样适用于容错域。  

使用多个升级域的缺点是升级需要花费的时间更长。 当一个升级域完成升级后，Service Fabric 会等待一小段时间，并在开始升级下一个域之前执行检查。 通过这一延迟时间，可在继续升级之前检测升级带来的问题。 这种折衷是可接受的，因为可以防止错误的更改一次性影响过多的服务。

升级域太少会产生负面影响。 当每个升级域关闭并进行升级时，大部分的总体容量将不可用。 例如，如果你只有 3 个升级域，则一次只能采用整体服务或群集容量的 1/3。 因为你需要在群集的其余部分中有足够的容量来处理工作负荷，所以不需要同时关闭多个服务。 维护该缓冲区意味着在正常工作期间，这些节点比其他情况下的负载更少。 这会增加运行服务的成本。

环境中容错或升级域的总数没有实际限制，对于它们如何重叠也没有约束。 但是，存在一些通用的模式：

- 容错域和升级域按 1:1 映射
- 每个节点一个升级域（物理或虚拟 OS 实例）
- "条带化" 或 "矩阵" 模型，其中的容错域和升级域构成一台计算机，其中的计算机通常按对角线

<center>

![容错域和升级域的布局][Image4]
</center>

对于要选择的布局没有最佳答案。 每个选项都有各自的利与弊。 例如，1FD:1UD 模型很容易设置。 为每个节点配置一个升级域似乎是最常用的模型。 升级过程中会独立更新每个节点。 这类似于过去手动升级少量计算机的方式。

最常见的模型是 FD/UD 矩阵，其中容错域和升级域构成一个表，节点沿着对角线开始放置。 这是 Azure 中 Service Fabric 群集默认使用的模型。 对于具有多个节点的群集，最终都会形成与密集矩阵模式类似的模式。

> [!NOTE]
> 托管在 Azure 中的 Service Fabric 群集不支持更改默认策略。 只有独立群集提供这种自定义。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容错域与升级域约束及最终行为
### <a name="default-approach"></a>默认方法
默认情况下，群集资源管理器维持服务在容错域和升级域之间的平衡。 这将建模为[约束](service-fabric-cluster-resource-manager-management-integration.md)。 容错域和升级域的约束： "对于给定的服务分区，在同一层次结构级别的任意两个域之间的服务对象数不应超过1个 (无状态服务实例或有状态服务副本) 。"

假设此约束提供 "最大差异" 保证。 容错域和升级域的约束会阻止违反该规则的某些移动或排列操作。

例如，假设有一个 6 节点群集，其中配置了 5 个容错域和 5 个升级域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

现在，假设要创建一个 **TargetReplicaSetSize**（如果是无状态服务，则为 **InstanceCount**）值为 5 的服务。 副本驻留在 N1-N5 上。 事实上，无论创建多少个此类服务，都不会用到 N6。 为什么？ 看看当前布局和选择 N6 时所发生情况之间的差异。

下面是获得的布局，以及每个容错域和升级域的副本总数：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此布局在每个容错域与升级域的节点数目上是均衡的。 并且在每个容错域和升级域的副本数目上也是均衡的。 每个域都拥有相同数量的节点，以及相同数量的副本。

现在让我们看看，如果不使用 N2，而是改用 N6 会发生什么情况。 副本会如何分布？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

此布局违反了容错域约束的 "最大差异" 保证定义。 FD0 有 2 个副本，而 FD1 有 0 个。 FD0 与 FD1 之间的总差为 2，这个总差已大于最大差值 1。 由于违反了约束，群集资源管理器不允许这种排列方式。 同样，如果选择 N2 和 N6（而不是 N1 和 N2），则会得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

就容错域而言，此布局是均衡的。 但是，现在违反了升级域约束，因为 UD0 有 0 个副本，而 UD1 有 2 个。 此布局也是无效的，群集资源管理器不会选取此布局。

这种有状态副本或无状态实例的分布方法提供了最佳容错能力。 如果某个域关闭，只会丢失极少量的副本/实例。 

另一方面，此方法过于严格，不允许群集使用所有资源。 对于某些群集配置，某些节点不可用。 这可能导致 Service Fabric 无法放置服务，从而出现警告消息。 在上面的示例中，某些群集节点不能在示例)  (N6 中使用。 即使已将节点添加到该群集 (N7-N10) ，副本/实例也只会置于 N1 – N5 上，因为对容错域和升级域的约束。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>替代方法

群集资源管理器支持另一版本的容错域和升级域约束。 该版本在允许放置服务的同时仍然保证最低级别的安全。 可按如下方式声明备用约束： "对于给定的服务分区，跨域的副本分发应确保该分区不会遭受仲裁丢失。" 假设此约束提供 "仲裁安全" 保障。 

> [!NOTE]
> 对于有状态服务，如果大部分分区副本同时关闭，则表示“仲裁丢失”  。 例如，如果 **TargetReplicaSetSize** 为 5，则任意三个副本一组表示仲裁。 同样，如果 **TargetReplicaSetSize** 为 6，则仲裁必须要 4 个副本。 如果分区想要继续正常运行，则在这两种情况下，不能同时关闭超过两个副本。 
>
> 对于无状态服务，不存在类似仲裁丢失的情况。  即使大部分实例同时关闭，无状态服务也仍可继续正常运行。 因此，本文的余下内容将重点介绍有状态服务。
>

返回上一个示例。 对于约束的 "仲裁安全" 版本，所有三个布局都有效。 即使第二个布局中的 FD0 或第三个布局中的 UD1 发生故障，分区仍有仲裁。 （大部分副本仍可运行）。由于该版本的约束，几乎始终可以使用 N6。

"仲裁安全" 方法比 "最大差异" 方法具有更大的灵活性。 原因在于，更容易在几乎所有群集拓扑中找到有效的副本分布。 但是，此方法不能保证最佳容错特性，因为有些故障比其他故障更为严重。 

在最坏的情况下，大部分副本会因为某个域和某个额外的副本故障而丢失。 例如，相比 5 个副本或实例要求其中 3 个故障才丢失仲裁，现在仅 2 个故障就可能丢失大部分副本。 

### <a name="adaptive-approach"></a>自适应方法
由于这两种方法都有优缺点，现在我们将介绍结合了两种策略的自适应方法。

> [!NOTE]
> 从 Service Fabric 版本 6.2 起，这是默认行为。 
> 
> 自适应方法默认使用“最大差值”逻辑，并且仅在必要时切换为“仲裁安全”逻辑。 群集资源管理器通过查看群集和服务的配置方式，自动辨别必要的策略。
> 
> 群集资源管理器应将 "基于仲裁" 的逻辑用于服务这两个条件都成立：
>
> * 该服务的 **TargetReplicaSetSize** 能被容错域数和升级域数整除。
> * 节点数小于或等于容错域数乘以升级域数的积。
>
> 请注意，群集资源管理器对无状态和有状态服务都将使用此方法，即使仲裁丢失与无状态服务无关。

让我们回到前面的示例，假设群集现在有8个节点。 群集仍然配置了 5 个容错域和 5 个升级域，该群集上托管的服务的 **TargetReplicaSetSize** 仍为 5。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

由于满足了所有必需的条件，因此群集资源管理器将使用 "基于仲裁" 的逻辑来分发服务。 这将启用 N6-N8。 本例中的一个可能的服务分布如下所示：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

如果服务的**TargetReplicaSetSize**值减小到了四 (例如) ，则群集资源管理器会注意到更改。 它将使用 "最大差值" 逻辑恢复，因为**TargetReplicaSetSize**不会再 dividable 容错域和升级域的数目。 因此，为了将余下的 4 个副本分布在节点 N1-N5 上，将移动部分副本。 这样，就不会违反容错域和升级域逻辑的 "最大差异" 版本。 

在上面的布局中，如果 **TargetReplicaSetSize** 值为 5，并将 N1 从群集中删除，则升级域数将等于 4。 同样，群集资源管理器使用 "最大差异" 逻辑开始，因为升级域的数目不会再平均地划分服务的**TargetReplicaSetSize**值。 因此，再次构建副本 R1 时，它必须位于 N4 上，这样才不会违反容错域和升级域的约束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |不适用 |不适用 |不适用 |空值 |空值 |空值 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>配置容错域和升级域
在 Azure 托管的 Service Fabric 部署中，容错域和升级域是自动定义的。 Service Fabric 从 Azure 中选择并使用环境信息。

如果要创建自己的群集 (或想要在开发) 中运行特定拓扑，可以自行提供容错域和升级域信息。 在本示例中，我们定义了一个 9 节点本地开发群集，该群集跨 3 个数据中心（每个数据中心有 3 个机架）。 该群集还有跨这三个数据中心条带化的三个升级域。 下面是 ClusterManifest.xml 中的配置示例：

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

此示例使用 ClusterConfig.json 实现独立部署：

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
> 通过 Azure 资源管理器定义群集时，Azure 将分配容错域和升级域。 因此，Azure 资源管理器模板中节点类型和虚拟机规模集的定义不包含有关容错域或升级域的信息。
>

## <a name="node-properties-and-placement-constraints"></a>节点属性和放置约束
有时 (事实上，在大多数情况下) 你需要确保某些工作负荷仅在群集中特定类型的节点上运行。 例如，某些工作负荷可能需要 GPU 或 SSD，而有些则不用。 

一个有说服力的示例就是，几乎在每个 n 层体系结构，都有专门的硬件来处理特定的工作负荷。 某些计算机充当应用程序的前端或 API 服务端，并向客户端或在 Internet 上公开。 其他一些计算机（通常具有不同的硬件资源）处理计算或存储层的工作。 通常不会直接向客户端或 Internet 公开这些计算机  。 

Service Fabric 预期存在特定工作负荷需要在特定硬件配置上运行的情况。 例如：

* 现有的 n 层应用程序已 "提升并移动" 到 Service Fabric 环境中。
* 出于性能、规模或安全性隔离原因，某个工作负荷必须在特定硬件上运行。
* 出于策略或资源消耗原因，某个工作负荷应该与其他工作负荷隔离。

为了支持此类配置，Service Fabric 包含可应用于节点的标记。 这些标记称为*节点属性*。 放置约束是附加到单个服务的语句，这些服务专供 1 个或多个节点属性选择。  放置约束定义服务运行的位置。 约束集可扩展。 任何键/值对都适用。 

<center>

![群集布局的不同工作负荷][Image5]
</center>

### <a name="built-in-node-properties"></a>内置节点属性
Service Fabric 定义了一些可自动使用的默认节点属性，你无需定义这些属性。 在每个节点上定义的默认属性是 **NodeType** 和 **NodeName**。 

例如，可将放置约束编写为 `"(NodeType == NodeType03)"`。 **NodeType** 是一个常用的属性。 它很有用，因为它与计算机的类型之间存在一一对应关系。 每种计算机类型都与一种传统 n 层应用程序的工作负荷类型相对应。

<center>

![放置约束和节点属性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置约束和节点属性语法 
节点属性中指定的值可以是字符串、布尔值或带符号的长型值。 服务处的语句称为放置约束，它可以约束服务在群集中的运行位置  。 约束可以是针对群集中的节点属性运行的任何布尔值语句。 这些布尔值语句中的有效选择器为：

* 用于创建特定语句的条件检查：

  | 语句 | 语法 |
  | --- |:---:|
  | “等于” | "==" |
  | “不等于” | "!=" |
  | “大于” | ">" |
  | “大于等于” | ">=" |
  | “小于” | "<" |
  | “小于等于” | "<=" |

* 用于分组和逻辑运算的布尔值语句：

  | 语句 | 语法 |
  | --- |:---:|
  | “和” | "&&" |
  | “或” | "&#124;&#124;" |
  | “非” | "!" |
  | “分组为单个语句” | "()" |

下面是基本约束语句的一些示例：

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

只有整个放置约束语句求值为“True”的节点才能放置服务。 未定义属性的节点不匹配包含该属性的任何放置约束。

假设为 ClusterManifest.xml 中的节点类型定义了以下节点属性：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

以下示例演示了通过 ClusterConfig.json 为独立部署定义的，或者通过 Template.json 为 Azure 托管群集定义的节点属性。 

> [!NOTE]
> 在 Azure 资源管理器模板中，节点类型通常已参数化。 它类似于 `"[parameters('vmNodeType1Name')]"` 而不是 NodeType01。
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

可以针对服务创建服务放置约束，如下所示  ：

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

如果 NodeType01 的所有节点都有效，则也可以使用约束 `"(NodeType == NodeType01)"` 选择该节点类型。

在运行时，服务的放置约束可以动态更新。 如果需要，可以在群集中移动服务、添加和删除要求，等等。 Service Fabric 确保即使进行了这些类型的更改，服务仍保持运行且可供使用。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置约束是针对每个命名服务实例指定的。 更新始终会取代（覆盖）以前指定的值。

群集定义用于定义节点上的属性。 更改节点属性需要升级群集配置。 升级节点属性需要重启每个受影响的节点以报告其新属性。 Service Fabric 会管理这些滚动升级。

## <a name="describing-and-managing-cluster-resources"></a>描述和管理群集资源
任何协调器的最重要作业之一是帮助管理群集中的资源消耗。 而管理群集资源时需要注意的事项有所不同。 

首先，确保计算机不会过载。 这是指确保计算机运行的服务数不超过其可处理的服务数。 

其次，需要权衡和优化与高效运行服务息息相关的因素。 经济有效型或性能敏感型服务产品不允许某些节点处于热状态，而其他节点处于冷状态。 热节点会导致资源争用和性能不佳。 冷节点意味着资源浪费和成本增加。 

Service Fabric 使用“指标”表示资源。  指标是要向 Service Fabric 描述的任何逻辑或物理资源。 指标的示例包括 "WorkQueueDepth" 或 "MemoryInMb"。 若要了解 Service Fabric 可在节点上调控的物理资源，请参阅[资源调控](service-fabric-resource-governance.md)。 若要了解群集资源管理器使用的默认指标以及如何配置自定义指标，请参阅[此文](service-fabric-cluster-resource-manager-metrics.md)。

指标与放置约束和节点属性不同。 节点属性是节点自身的静态描述符。 指标描述节点所含资源，以及当服务在节点上运行时服务所消耗的资源。 节点属性可能为 **HasSSD**，可设置为 true 或 false。 该 SSD 上的可用空间量和服务消耗的空间量是类似于 "DriveSpaceInMb" 的指标。 

与放置约束和节点属性相同，Service Fabric 群集资源管理器不理解指标名称的含义。 指标名称只是字符串。 建议不明确时，将单位声明为创建的指标名称的一部分。

## <a name="capacity"></a>容量
如果关闭所有的资源均衡功能，Service Fabric 群集资源管理器仍会确保最终不会有任何节点超出其容量  。 对容量溢出进行管理是可能的，除非群集过于饱和或工作负荷大于任何节点。 容量是群集资源管理器用来了解节点包含的资源量的另一个约束  。 还会针对整个群集来追踪剩余容量。 

服务级别的容量和消耗量均以指标来表示。 例如，指标可能是“ClientConnections”，节点可能拥有 32,768 个单位的“ClientConnections”容量。 其他节点可能有其他限制。 在该节点上运行的某个服务可以声明其当前正在消耗 32,256 个单位的“ClientConnections”指标。

在运行时，群集资源管理器会跟踪群集中和节点上的剩余容量。 群集资源管理器通过从运行服务的节点容量中减去每个服务的使用量来跟踪容量。 利用此信息，群集资源管理器可以确定放置或移动副本的位置，使节点不会超出容量。

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

可以在群集清单中看到定义的容量。 下面是 ClusterManifest.xml 的示例：

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

下面是通过 ClusterConfig.json 为独立部署定义的，或者通过 Template.json 为 Azure 托管群集定义的容量示例： 

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

服务负载通常会动态更改。 假设某个副本的“ClientConnections”负载从 1,024 更改为 2,048。 当时正在运行该副本的节点只剩余了 512 个单位的该指标容量。 现在副本或实例的位置无效，因为该节点上没有足够的空间。 群集资源管理器必须使节点上的容量消耗重新低于阈值。 这可通过将一个或多个副本或实例从该节点转移到其他节点，来减少超出容量的节点上的负载。 

移动副本时，群集资源管理器会尝试将移动成本降至最低。 你可以深入了解[移动成本](service-fabric-cluster-resource-manager-movement-cost.md)及[重新均衡策略和规则](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="cluster-capacity"></a>群集容量
Service Fabric 群集资源管理器如何防止整个群集过于饱和？ 使用动态负载时，它没有太大的意义。 服务可使自己的负载高峰独立于群集资源管理器所执行的操作。 因此，群集当前或许拥有足够的容量，但将来需要扩大规模时，可能就不够用了。 

群集资源管理器中的一些控制措施有助于防止问题。 可做的第一件事是防止创建导致群集空间变满的新工作负荷。

假设要创建一个无状态服务，并且它有某些关联的负载。 服务需考虑“DiskSpaceInMb”指标。 服务的每个实例将消耗 5 个单位的“DiskSpaceInMb”。 需要创建服务的 3 个实例。 这意味着，群集中需要有 15 个单位的“DiskSpaceInMb”才能创建这些服务实例。

群集资源管理器会持续计算容量和每个指标的消耗量，因此它可以确定群集中的剩余容量。 如果没有足够的空间，群集资源管理器会拒绝创建服务的调用。

因为只要求有 15 个可用单位，所以可以使用多种不同的方式分配此空间。 例如，可能是在 15 个不同节点上各有一个剩余单位的容量，或是在 5 个不同节点上各有三个剩余单位的容量。 如果群集资源管理器能够重新排列服务，在 3 个节点上提供 5 个单位，则可放置服务。 重新排列群集通常是可行的，除非群集几乎已满，或者出于某种原因无法合并现有服务。

## <a name="buffered-capacity"></a>缓冲容量
缓冲容量是群集资源管理器的另一项功能。 使用此功能可以预留总节点容量的一部分。 此容量缓冲区仅用于在升级期间和发生节点故障时放置服务。 

缓冲容量是全局指定的，即针对所有节点按指标指定的。 为保留容量选择的值取决于群集中的容错域和升级域数目。 较多的容错域和升级域意味着可以选择较少数值的缓冲处理容量。 域越多，则升级和故障过程中无法使用的群集数量越少。 指定缓冲容量只有在同时指定了指标的节点容量时才有意义。

以下示例演示如何在 ClusterManifest.xml 中指定缓冲容量：

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

以下示例演示如何通过 ClusterConfig.json 为独立部署定义的，或者通过 Template.json 为 Azure 托管群集指定缓冲容量：

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

群集用于某个指标的缓冲容量不足时，创建新服务会失败。 通过防止创建新服务来保留缓冲区，可确保升级和故障不会造成节点超出容量。 缓冲容量是可选项，但我们建议为定义了指标容量的所有群集启用。

群集资源管理器会公开此负载信息。 对于每个指标，此信息包括： 
- 缓冲容量设置。
- 总容量。
- 当前消耗量。
- 每项指标是否视为均衡。
- 有关标准偏差的统计信息。
- 负载最大和最小的节点。  
  
以下代码演示了该输出的示例：

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
* 有关群集资源管理器中的体系结构和信息流的信息，请参阅[群集资源管理器体系结构概述](service-fabric-cluster-resource-manager-architecture.md)。
* 定义重整指标是合并（而不是分散）节点上负载的一种方式。若要了解如何配置重整，请参阅 [Service Fabric 中指标和负载的重整](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 从 [Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)开始学习。
* 若要了解群集资源管理器如何在群集中管理和均衡负载，请参阅[均衡 Service Fabric 群集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
