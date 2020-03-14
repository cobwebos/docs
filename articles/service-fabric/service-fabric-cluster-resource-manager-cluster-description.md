---
title: 使用群集描述群集资源管理器
description: 通过为群集资源管理器指定容错域、升级域、节点属性和节点容量来描述 Service Fabric 群集。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258767"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>使用群集描述 Service Fabric 群集资源管理器
Azure Service Fabric 的群集资源管理器功能提供了用于描述群集的多种机制：

* 容错域
* 升级域
* 节点属性
* 节点容量

在运行时，群集资源管理器会使用此信息来确保群集中运行的服务的高可用性。 实施这些重要规则时，它还会尝试优化群集中的资源消耗。

## <a name="fault-domains"></a>容错域
容错域是协调故障的任何区域。 单台计算机是容错域。 由于各种原因，它可能会因各种原因而失败，原因是电源故障导致 NIC 固件出现故障。 

连接到同一以太网交换机的计算机位于同一个容错域中。 这是共享单个电源源的计算机，或者位于单个位置的计算机。 

由于硬件故障重叠，容错域本质上是分层的。 它们在 Service Fabric 中表示为 Uri。

正确设置容错域很重要，因为 Service Fabric 使用此信息来安全放置服务。 Service Fabric 不希望放置服务，使容错域丢失（因某些组件的故障而导致）会导致服务中断。 

在 Azure 环境中，Service Fabric 使用环境提供的容错域信息来以你的名义正确地配置群集中的节点。 对于 Service Fabric 的独立实例，将在设置群集时定义容错域。 

> [!WARNING]
> 向 Service Fabric 提供的容错域信息很重要。 例如，假设 Service Fabric 群集的节点在10个虚拟机内运行，在5个物理主机上运行。 在此情况下，即使有 10 个虚拟机，也只有 5 个不同的（顶层）容错域。 共享同一物理主机会导致 Vm 共享同一个根容错域，因为如果这些 Vm 的物理主机发生故障，它们会经历协调故障。  
>
> Service Fabric 需要节点的容错域不会更改。 确保 Vm 高可用性的其他机制（例如[HA vm](https://technet.microsoft.com/library/cc967323.aspx)）可能会导致与 Service Fabric 冲突。 这些机制使用 Vm 从一个主机到另一个主机的透明迁移。 它们不会重新配置或通知 VM 内运行的代码。 因此，*不支持*将它们用作运行 Service Fabric 群集的环境。 
>
> 应使用 Service Fabric 作为唯一的高可用性技术。 实时 VM 迁移和 San 之类的机制并不是必需的。 如果将这些机制与 Service Fabric 结合使用，则会_降低_应用程序的可用性和可靠性。 原因在于它们带来了额外的复杂性，添加了失败的集中源，并使用与 Service Fabric 中的资源冲突的可靠性和可用性策略。 
>
>

在下图中，我们将对容错域的所有实体进行着色，并列出生成的所有不同容错域。 本示例列出了数据中心 (DC)、机架 (R) 和刀片服务器 (B)。 如果每个边栏选项卡包含多个虚拟机，则容错域层次结构中可能有另一个层。

<center>

![通过容错域组织的节点][Image1]
</center>

在运行时，Service Fabric 群集资源管理器会考虑群集中的容错域和计划布局。 服务的有状态副本或无状态实例将被分发，因此它们位于不同的容错域中。 当容错域在层次结构的任何级别发生故障时，跨容错域分发服务可确保服务可用性不受影响。

群集资源管理器不在意容错域层次结构中有多少层。 它尝试确保失去层次结构的任何一个部分不会影响在其中运行的服务。 

最好是在容错域层次结构中的每个深度级别上具有相同的节点数。 如果群集中的容错域的 "树" 不均衡，则群集资源管理器更难确定最佳的服务分配。 不均衡容错域布局意味着某些域的损失会影响到其他域的服务可用性。 因此，群集资源管理器在两个目标之间有破损： 

* 它需要通过将服务放置在该 "繁重" 域中来使用这些计算机。 
* 它想要将服务放在其他域中，以便域丢失不会造成问题。 

失衡的域是什么样子？ 下图显示了两种不同的群集布局。 在第一个示例中，节点均匀分布在容错域中。 在第二个示例中，一个容错域包含的节点比其他容错域多。 

<center>

][Image2]
![两种不同的群集布局 </center>

在 Azure 中，可选择哪个容错域包含节点。 但根据你预配的节点数量，你仍可以获得比其他节点更多的容错域。 

例如，假设群集中有5个容错域，但针对节点类型（**NodeType**）预配了7个节点。 在这种情况下，前两个容错域最终会获得更多节点。 如果继续使用几个实例来部署更多的**NodeType**实例，则问题会变得更糟。 出于此原因，我们建议每个节点类型中的节点数为容错域数的倍数。

## <a name="upgrade-domains"></a>升级域
升级域是另一项功能，可帮助 Service Fabric 群集资源管理器了解群集的布局。 升级域定义同时升级的节点集。 升级域有助于群集资源管理器了解和安排诸如升级等管理操作。

升级域非常类似于容错域，但有几个重要的差异。 首先，协调硬件故障区域定义容错域。 另一方面，升级域由策略定义。 您可以确定所需的数量，而不是让环境规定数字。 你可以拥有任意数量的升级域来执行节点。 容错域与升级域之间的另一个区别在于，升级域不是分层的。 相反，它们更像是一个简单的标记。 

下图显示了三个容错域中的三个升级域。 它还为有状态服务的三个不同副本显示一个可能的位置，每个副本在不同的容错域和升级域中最后。 此位置允许在服务升级过程中丢失容错域，但仍有一个代码和数据副本。  

<center>

容错域和升级域的 ![放置][Image3]
</center>

有很多升级域的优点和缺点。 更多升级域意味着升级的每个步骤都更细化，并影响更少的节点或服务。 一次只需移动较少的服务，从而减少系统中的变动。 这往往会提高可靠性，因为在升级过程中引入的任何问题都不会影响服务。 更多升级域也意味着，在其他节点上需要较少的可用缓冲区来处理升级的影响。 

例如，如果有五个升级域，则每个域中的节点将处理大约20% 的流量。 如果需要关闭升级域进行升级，则通常需要将该负载转到某个位置。 由于你有四个剩余升级域，因此每个升级域都必须具有大约5% 的总流量。 更多升级域意味着群集中节点上的缓冲区更少。 

请考虑是否改为10个升级域。 在这种情况下，每个升级域只会处理大约10% 的总流量。 当升级步骤通过群集时，每个域只需有大约1.1% 的总流量。 更多升级域通常允许以更高的利用率运行节点，因为需要保留的容量更少。 这同样适用于容错域。  

具有很多升级域的缺点是升级往往需要更长的时间。 Service Fabric 在升级域完成之后会等待一小段时间，并在开始升级下一个域之前执行检查。 通过这一延迟时间，可在继续升级之前检测升级带来的问题。 这种折衷是可接受的，因为可以防止错误的更改一次性影响过多的服务。

升级域太少会产生许多负面影响。 当每个升级域关闭并进行升级时，整体容量中的大部分容量均不可用。 例如，如果只有三个升级域，则每次都要停止整体服务或群集容量的三分之一。 因为你需要在群集的其余部分中有足够的容量来处理工作负荷，所以不需要同时关闭多个服务。 维护该缓冲区意味着在正常操作期间，这些节点比其他情况下的负载更少。 这会增加运行服务的成本。

环境中容错或升级域的总数没有实际限制，对于它们如何重叠也没有约束。 但有一些常见模式：

- 容错域和升级域映射1:1
- 每个节点一个升级域（物理或虚拟 OS 实例）
- "条带化" 或 "矩阵" 模型，其中的容错域和升级域构成一台计算机，其中的计算机通常按对角线

<center>

容错域和升级域][Image4]
![布局 </center>

对于要选择的布局没有最佳答案。 每个选项都有各自的利与弊。 例如，1FD:1UD 模型很容易设置。 每个节点的一个升级域模型最类似于用户使用的模型。 在升级过程中，每个节点都单独更新。 这类似于过去手动升级少量计算机的方式。

最常见的模型是 FD/UD 矩阵，其中的容错域和升级域构成一个表，节点沿着对角线开始放置。 这是 Azure 中 Service Fabric 群集默认使用的模型。 对于包含多个节点的群集，最终看起来就像密集矩阵模式一样。

> [!NOTE]
> 托管在 Azure 中的 Service Fabric 群集不支持更改默认策略。 只有独立群集提供该自定义项。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容错域与升级域约束及最终行为
### <a name="default-approach"></a>默认方法
默认情况下，群集资源管理器在容错域和升级域之间保持服务平衡。 这将建模为[约束](service-fabric-cluster-resource-manager-management-integration.md)。 容错域和升级域的约束状态： "对于给定的服务分区，在同一台服务器上的任意两个域之间的服务对象（无状态服务实例或有状态服务副本）数目中，不应超过1个层次结构的级别。 "

假设此约束提供 "最大差异" 保证。 容错域和升级域的约束会阻止违反规则的某些移动或排列。

例如，假设有一个包含六个节点的群集，其中配置了5个容错域和5个升级域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

现在，假设我们使用**TargetReplicaSetSize** （或无状态服务， **InstanceCount**）值5创建了一个服务。 副本驻留在 N1-N5 上。 事实上，无论创建多少个此类服务，都不会用到 N6。 为什么？ 让我们看看 N6 与当前布局之间的差异，以及选择 N6 时会发生情况。

下面是我们获得的布局，以及每个容错域和升级域的副本总数：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此布局根据每个容错域和升级域的节点数进行平衡。 它还根据每个容错域和升级域的副本数进行了平衡。 每个域都拥有相同数量的节点，以及相同数量的副本。

现在，让我们看看如果使用 N6 而不是 N2，会发生什么情况。 副本将如何分布？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

此布局违反了容错域约束的 "最大差异" 保证定义。 FD0 有两个副本，而 FD1 的值为零。 FD0 和 FD1 之间的差异总计是两个，大于其最大差值。 由于违反了约束，因此群集资源管理器不允许这种排列。 同样，如果选择 N2 和 N6 （而不是 N1 和 N2），则会得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此布局根据容错域平衡。 但现在，它违反了升级域约束，因为 UD0 有零个副本，而 UD1 有两个副本。 此布局也是无效的，群集资源管理器不会选取此布局。

这种有状态副本或无状态实例的分布方法提供了最佳容错能力。 如果一个域出现故障，副本/实例的数量就会下降。 

另一方面，此方法过于严格，不允许群集使用所有资源。 对于某些群集配置，某些节点不可用。 这可能会导致 Service Fabric 不会导致出现警告消息。 在上面的示例中，无法使用某些群集节点（示例中的 N6）。 即使已将节点添加到该群集（N7-N10），副本/实例也只会置于 N1 – N5 上，因为对容错域和升级域的约束。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>替代方法

群集资源管理器支持容错域和升级域约束的另一版本。 它允许放置，同时仍保证最小安全级别。 可按如下方式声明备用约束： "对于给定的服务分区，跨域的副本分发应确保该分区不会遭受仲裁丢失。" 假设此约束提供 "仲裁安全" 保障。 

> [!NOTE]
> 对于有状态服务，如果大部分分区副本同时关闭，则表示“仲裁丢失”。 例如，如果**TargetReplicaSetSize**为5，则任何三个副本的一组表示仲裁。 同样，如果**TargetReplicaSetSize**为6，则仲裁需要四个副本。 在这两种情况下，如果分区要继续正常运行，则不能同时关闭两个以上的副本。 
>
> 对于无状态服务，不会出现*仲裁丢失*的情况。 即使大多数实例同时关闭，无状态服务仍可继续正常工作。 那么，我们将重点介绍本文其余部分的有状态服务。
>

返回上一个示例。 对于约束的 "仲裁安全" 版本，所有三个布局都有效。 即使第二个布局中的 FD0 失败或第三个布局中的 UD1 失败，分区仍将具有仲裁。 （大多数副本仍会启动。）使用此版本的约束，几乎始终可以使用 N6。

"仲裁安全" 方法比 "最大差异" 方法具有更大的灵活性。 原因在于，查找几乎在任何群集拓扑中有效的副本分发更容易。 但是，此方法不能保证最佳容错特性，因为有些故障比其他故障更为严重。 

在最坏的情况下，大多数副本可能会丢失，同时出现一个域和一个其他副本。 例如，如果丢失包含5个副本或实例的仲裁所需的三个故障，你现在可以丢失多数，只发生两个故障。 

### <a name="adaptive-approach"></a>自适应方法
由于这两种方法都有优点和缺点，因此我们引入了一种自适应方法，组合了这两个策略。

> [!NOTE]
> 这是 Service Fabric 版本6.2 开始的默认行为。 
> 
> 自适应方法默认使用“最大差值”逻辑，并且仅在必要时切换为“仲裁安全”逻辑。 群集资源管理器通过查看群集和服务的配置方式，自动找出所需的策略。
> 
> 群集资源管理器应将 "基于仲裁" 的逻辑用于服务这两个条件都成立：
>
> * 此服务的**TargetReplicaSetSize**可被容错域数和升级域数整除。
> * 节点数小于或等于容错域的数量乘以升级域的数量。
>
> 请记住，群集资源管理器会将此方法用于无状态服务和有状态服务，即使仲裁丢失与无状态服务无关。

让我们回到前面的示例，假设群集现在有8个节点。 群集仍配置有5个容错域和5个升级域，而该群集上托管的服务的**TargetReplicaSetSize**值仍然为5。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

由于满足了所有必需的条件，因此群集资源管理器将使用 "基于仲裁" 的逻辑来分发服务。 这样就可以使用 N6-N8。 在这种情况下，一个可能的服务分发如下所示：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

如果服务的 " **TargetReplicaSetSize** " 值减少到 "4" （例如），则群集资源管理器会注意到更改。 它将使用 "最大差值" 逻辑恢复，因为**TargetReplicaSetSize**不会再 dividable 容错域和升级域的数目。 因此，会发生某些副本移动，以将其余四个副本分发到节点 N1-N5 上。 这样，就不会违反容错域和升级域逻辑的 "最大差异" 版本。 

在前面的布局中，如果**TargetReplicaSetSize**值为5，并从群集中删除 N1，则升级域的数量将等于4。 同样，群集资源管理器使用 "最大差异" 逻辑开始，因为升级域的数目不会再平均地划分服务的**TargetReplicaSetSize**值。 因此，在重新生成时，副本 R1 必须位于 N4 上，因此不会违反容错域和升级域的约束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |空值 |空值 |空值 |空值 |空值 |空值 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>配置容错域和升级域
在 Azure 托管的 Service Fabric 部署中，会自动定义容错域和升级域。 Service Fabric 从 Azure 中选择并使用环境信息。

如果要创建自己的群集（或想要在开发环境中运行特定的拓扑），可以自行提供容错域和升级域信息。 在此示例中，我们定义了一个9节点本地开发群集，该群集跨三个数据中心（每个数据中心有三个数据中心）。 此群集还具有三个跨这三个数据中心条带化的升级域。 下面是 Clustermanifest.xml 中的配置示例：

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

此示例对独立部署使用 Clusterconfig.x509.multimachine.json：

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
> 通过 Azure 资源管理器定义群集时，Azure 会分配容错域和升级域。 因此，Azure 资源管理器模板中节点类型和虚拟机规模集的定义不包含有关容错域或升级域的信息。
>

## <a name="node-properties-and-placement-constraints"></a>节点属性和放置约束
有时（事实上，在大多数情况下），您需要确保某些工作负荷只能在群集中特定类型的节点上运行。 例如，某些工作负荷可能需要 Gpu 或 Ssd，而其他工作负荷可能不需要。 

面向特定工作负荷的硬件的一个很好的示例几乎是每个 n 层体系结构。 某些计算机充当应用程序的前端或 API 服务端，并向客户端或 internet 公开。 其他一些计算机（通常具有不同的硬件资源）处理计算或存储层的工作。 通常不会直接向客户端或 Internet 公开这些计算机。 

Service Fabric 要求在某些情况下，特定的工作负荷可能需要在特定硬件配置上运行。 例如：

* 现有的 n 层应用程序已 "提升并移动" 到 Service Fabric 环境中。
* 出于性能、规模或安全隔离的原因，工作负荷必须在特定硬件上运行。
* 出于策略或资源消耗原因，工作负荷应该与其他工作负荷隔离开来。

为了支持这些类型的配置，Service Fabric 包含可应用于节点的标记。 这些标记称为节点属性。 *放置约束*是附加到单个服务的语句，这些服务是您为一个或多个节点属性选择的。 放置约束定义服务运行的位置。 约束集是可扩展的。 任何键/值对都可以工作。 

<center>

为群集布局][Image5]
![不同的工作负荷 </center>

### <a name="built-in-node-properties"></a>内置节点属性
Service Fabric 定义一些默认节点属性，这些属性可以自动使用，因此无需对其进行定义。 在每个节点上定义的默认属性是**NodeType**和**NodeName**。 

例如，可以将放置约束编写为 `"(NodeType == NodeType03)"`。 **NodeType**是一个常用的属性。 它很有用，因为它对应于一种计算机类型的1:1。 每种计算机类型都与一种传统 n 层应用程序的工作负荷类型相对应。

<center>

![放置约束和节点属性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置约束和节点属性语法 
节点属性中指定的值可以是字符串、布尔值或带符号的 long。 服务中的语句称为放置*约束*，因为它限制服务可以在群集中运行的位置。 约束可以是针对群集中的节点属性运行的任何布尔值语句。 这些布尔值语句中的有效选择器是：

* 用于创建特定语句的条件检查：

  | 语句 | 语法 |
  | --- |:---:|
  | “等于” | "==" |
  | “不等于” | "!=" |
  | “大于” | ">" |
  | “大于等于” | ">=" |
  | “小于” | "<" |
  | “小于等于” | "<=" |

* 用于分组和逻辑运算的布尔语句：

  | 语句 | 语法 |
  | --- |:---:|
  | “和” | "&&" |
  | “或” | "&#124;&#124;" |
  | “非” | "!" |
  | “组合成单个语句” | "()" |

下面是基本约束语句的一些示例：

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

只有整个放置约束语句求值为“True”的节点才能放置服务。 未定义属性的节点不匹配包含该属性的任何放置约束。

假设为 Clustermanifest.xml 中的节点类型定义了以下节点属性：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

下面的示例演示了通过 Clusterconfig.x509.multimachine.json 定义的节点属性，这些属性通过用于独立部署的或 Azure 托管的群集的 Template。 

> [!NOTE]
> 在 Azure 资源管理器模板中，节点类型通常是参数化的。 它看起来像 `"[parameters('vmNodeType1Name')]"` 而不是 NodeType01。
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

您可以为服务创建服务放置*约束*，如下所示：

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

如果 NodeType01 的所有节点都是有效的，则还可以选择具有约束 `"(NodeType == NodeType01)"`的节点类型。

在运行时，服务的放置约束可以动态更新。 如果需要，可以在群集中移动服务、添加和删除要求，等等。 Service Fabric 可确保即使进行了这些类型的更改，服务仍保持运行并可用。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置约束是针对每个命名服务实例指定的。 更新始终会取代（覆盖）以前指定的值。

群集定义用于定义节点上的属性。 更改节点属性需要升级群集配置。 升级节点属性需要重启每个受影响的节点以报告其新属性。 Service Fabric 管理这些滚动升级。

## <a name="describing-and-managing-cluster-resources"></a>描述和管理群集资源
任何协调器的最重要作业之一是帮助管理群集中的资源消耗。 而管理群集资源时需要注意的事项有所不同。 

首先，确保计算机不会过载。 这是指确保计算机运行的服务数不超过其可处理的服务数。 

其次，有平衡和优化，这对于有效地运行服务至关重要。 经济高效或性能敏感的服务产品不允许某些节点处于热状态，而其他节点是冷的。 热节点会导致资源争用和性能不佳。 冷节点表示浪费资源并增加成本。 

Service Fabric 将资源表示为*指标*。 指标是你想要向 Service Fabric 描述的任何逻辑或物理资源。 指标的示例包括 "WorkQueueDepth" 或 "MemoryInMb"。 有关 Service Fabric 可以管理节点的物理资源的信息，请参阅[资源调控](service-fabric-resource-governance.md)。 有关群集使用的默认指标资源管理器以及如何配置自定义度量值的信息，请参阅[此文](service-fabric-cluster-resource-manager-metrics.md)。

指标与放置约束和节点属性不同。 节点属性是节点自身的静态描述符。 指标描述节点所具有的资源以及服务在节点上运行时所使用的资源。 节点属性可能**HasSSD** ，可能会设置为 true 或 false。 该 SSD 上的可用空间量和服务消耗的空间量是类似于 "DriveSpaceInMb" 的指标。 

与放置约束和节点属性一样，Service Fabric 群集资源管理器不了解指标的名称。 指标名称只是一些字符串。 最好将单位声明为你创建的指标名称的一部分，在这些名称可能是不明确的。

## <a name="capacity"></a>容量
如果关闭所有资源*均衡*，Service Fabric 群集资源管理器仍将确保没有节点超出其容量。 对容量溢出进行管理是可能的，除非群集过于饱和或工作负荷大于任何节点。 容量是群集资源管理器用来了解节点具有的资源量的另一个*约束*。 系统还会跟踪整个群集的剩余容量。 

服务级别的容量和消耗量均以指标来表示。 例如，指标可能是 "ClientConnections"，并且节点的 "ClientConnections" 容量可能为32768。 其他节点可以有其他限制。 在该节点上运行的服务可能会说，当前正在使用 "ClientConnections" 指标的32256。

在运行时，群集资源管理器跟踪群集和节点上的剩余容量。 为了跟踪容量，群集资源管理器从运行服务的节点容量中减去每个服务的使用情况。 利用此信息，群集资源管理器可以确定放置或移动副本的位置，使节点不会超出容量。

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

你可以看到在群集清单中定义的容量。 下面是 Clustermanifest.xml 的示例：

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

下面是通过 Clusterconfig.x509.multimachine.json 定义的容量示例，用于独立部署或 Azure 托管群集的 json 模板： 

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

服务负载通常会动态更改。 假设副本的 "ClientConnections" 负载从1024更改为2048。 它在其上运行的节点的容量仅为该指标的剩余512。 由于副本或实例的位置无效，因此该节点上没有足够的空间。 群集资源管理器必须使节点返回低于容量。 它通过将一个或多个副本或实例从该节点移到其他节点来减少节点上的负载。 

群集资源管理器尝试最大程度地降低移动副本的成本。 你可以了解有关[移动成本](service-fabric-cluster-resource-manager-movement-cost.md)的详细信息以及重新[平衡策略和规则](service-fabric-cluster-resource-manager-metrics.md)的详细信息。

## <a name="cluster-capacity"></a>群集容量
Service Fabric 群集如何资源管理器使整个群集保持太满？ 使用动态负载时，它没有太大的意义。 服务可以使其负载高峰与群集资源管理器所需的操作无关。 这样一来，如果明天有任何余地，现在就可以用户成名之后群集。 

群集中的控件资源管理器有助于防止出现问题。 您可以做的第一件事是防止创建导致群集变满的新工作负荷。

假设您创建了一个无状态服务，并且它有一些相关联的负载。 该服务关心的是 "DiskSpaceInMb" 指标。 服务将为服务的每个实例使用5个单位的 "DiskSpaceInMb"。 我们需要创建 3 个服务实例。 这意味着，在群集中需要15个单位的 "DiskSpaceInMb" 才能创建这些服务实例。

群集资源管理器会持续计算每个指标的容量和消耗，因此它可以确定群集中的剩余容量。 如果没有足够的空间，则群集资源管理器拒绝对创建服务的调用。

由于要求仅提供15个单位，因此可以通过多种不同的方式分配此空间。 例如，在15个不同的节点上可能有一个剩余容量单位，或5个不同节点上有三个剩余容量单元。 如果群集资源管理器可以重新排列操作，以便三个节点上有五个单位，则会放置服务。 重新排列群集通常是可行的，除非群集几乎已满，或者出于某种原因无法合并现有服务。

## <a name="buffered-capacity"></a>缓冲容量
缓冲容量是群集资源管理器的另一项功能。 使用此功能可以预留总节点容量的一部分。 此容量缓冲区仅用于在升级和节点故障时放置服务。 

为所有节点的每个指标全局指定缓冲容量。 为保留容量选择的值是群集中的容错域和升级域的数目。 更多的容错域和升级域意味着可以为缓冲容量选择较小的数字。 如果域数较多，在升级期间和发生故障时不可用的群集数很可能较少。 指定缓冲容量只有在您还指定了指标的节点容量时才有意义。

下面的示例演示如何在 Clustermanifest.xml 中指定缓冲容量：

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

下面是一个示例，演示如何通过 Clusterconfig.x509.multimachine.json 为 Azure 托管的群集指定缓冲容量：

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

创建新服务会在群集耗尽指标的缓冲容量时失败。 通过防止创建新服务来保留缓冲区，可确保升级和故障不会造成节点超出容量。 缓冲容量是可选的，但我们建议在定义指标容量的任何群集中使用它。

群集资源管理器公开此负载信息。 对于每个指标，此信息包括： 
- 缓冲容量设置。
- 总容量。
- 当前消耗量。
- 每个指标是否视为均衡的。
- 有关标准偏差的统计信息。
- 负载最大和最小的节点。  
  
以下代码显示了该输出的示例：

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
* 定义碎片整理指标是合并节点上的负载而不是将其分散的一种方法。若要了解如何配置碎片整理，请参阅[对指标进行碎片整理和 Service Fabric 中的负载](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 从一开始就开始[介绍 Service Fabric 群集资源管理器的简介](service-fabric-cluster-resource-manager-introduction.md)。
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请参阅[平衡 Service Fabric 群集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
