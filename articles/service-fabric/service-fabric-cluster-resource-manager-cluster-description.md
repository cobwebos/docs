---
title: "资源平衡器群集描述 | Microsoft 文档"
description: "通过在群集资源管理器中指定容错域、升级域、节点属性和节点容量来描述 Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: a595a2e24b4c2582998d88f74ca13daba5fe618d
ms.lasthandoff: 02/17/2017

---

# <a name="describing-a-service-fabric-cluster"></a>描述 Service Fabric 群集
Service Fabric 群集资源管理器提供多种机制用于描述群集。 在运行时，群集资源管理器使用此信息来确保群集中运行的服务的高可用性。 实施这些重要的规则时，群集资源管理器还会尝试优化群集的资源消耗。

## <a name="key-concepts"></a>关键概念
群集资源管理器支持多种用于描述群集的功能：

* 容错域
* 升级域
* 节点属性
* 节点容量

## <a name="fault-domains"></a>容错域
容错域是协调故障的任何区域。 单一计算机就是一个容错域（因为它本身可能出于各种原因而发生故障，包括电源故障、驱动器故障、NIC 固件错误，等等）。 连接到同一以太网交换机的计算机位于同一个容错域中，共享一个电源的计算机也是如此。 由于硬件故障在性质上是重叠的，容错域原本就有层次性，在 Service Fabric 中以 URI 的形式表示。

设置自己的群集时，需要通盘考虑故障的不同层面。 确保正确设置容错域，因为 Service Fabric 使用这些信息来安全放置服务。 Service Fabric 不希望在放置服务后，容错域的中断（由某个组件的故障而导致）会造成服务关闭。 在 Azure 环境中，Service Fabric 使用环境提供的容错域信息来自动正确配置群集中的节点。

在下图中，我们已将构成容错域的所有实体着色，并列出了生成的所有不同容错域。 本示例列出了数据中心 (DC)、机架 (R) 和刀片服务器 (B)。 可以想象，如果每个刀片服务器包含多个虚拟机，则容错域层次结构中可能有另一个层。

<center>
![通过故障域组织的节点][Image1]
</center>

在运行时，Service Fabric 群集资源管理器会考虑群集中的容错域，并规划一种布局。  它会尝试分散给定服务的有状态副本和无状态副本，以便将它们放置在独立的容错域中。 在任何一个容错域（位于层次结构中的任何一个级别）发生故障时，此过程有助于确保该服务的可用性不遭到破坏。

Service Fabric 群集资源管理器不考虑容错域层次结构中有多少层。 不过，它会尝试确保丢失层次结构的任何一部分不至于影响到在其上运行的服务。 因此，最好是在容错域层次结构中的每个深度级别部署相同数目的节点。 保持级别的均衡可以防止层次结构的某个部分包含的服务比其他部分要多。 否则，各个节点的负载将会失衡，导致某些域的故障比其他域更加严重。

如果群集中的容错域“树”不均衡，群集资源管理器将更难找到最佳的服务分配。 容错域布局失衡意味着丢失特定的域对群集可用性造成的影响可能比丢失其他域要严重。 因此，群集资源管理器很难在两个目标之间作出取舍：是要通过将服务放置在该“繁重”域中来使用计算机，还是要放置服务来避免域的丢失造成问题。 这种布局怎么样？

<center>
![两种不同的群集布局][Image2]
</center>

上图显示了两种不同的群集布局示例。 在第一个示例中，节点均匀分散到容错域。 在另一个示例中，容错域包含许多额外的节点。 如果你曾经在本地或另一个环境中维护自己的群集，则需要注意一些事项。

在 Azure 中，系统将为你做出哪个节点位于哪个容错域的选择。 但是，根据预配的节点数目，某些容错域包含的节点可能仍然比其他容错域要多。 例如，假设你有&5; 个容错域，但针对 NodeType 预配了&7; 个节点。 在这种情况下，前两个容错域最终包含的节点要比其他容错域多。 如果继续部署更多的、只包含几个实例的 NodeType，问题会变得更糟。

## <a name="upgrade-domains"></a>升级域
升级域是另一项功能，可帮助 Service Fabric 群集资源管理器了解群集的布局，使它可以针对故障提前做出规划。 升级域定义同时升级的节点集。

升级域非常类似于容错域，但有几个重要的差异。 首先，容错域由协调故障区域严格定义，升级域由策略定义。 对于升级域，必须确定所需的数量，而不是由环境描述所需的数量。 另一个差异在于（截至目前为止），升级域不是分层的 – 更像是简单的标记。

下图显示了在三个容错域之间条带化的三个升级域。 该图还显示了有状态服务的三个不同副本的一种可能的放置方式，这些副本最终位于不同的容错域和升级域。 这种放置方式容许我们在服务升级中途丢失一个容错域，在这种情况下仍可运行一个代码和数据副本。

<center>
![包含容错域和升级域的布局][Image3]
</center>

使用大量升级域既有利也有弊。 使用较多升级域的优点是升级的每个步骤更细微，因此会给较少的节点或服务造成影响。 这会减少每次需要移动的服务数，降低系统中的流动。 此外，这往往会提高可靠性（因为在升级过程中发生的任何问题所影响到的服务更少）。 增加升级域也意味着在处理升级的影响时，在其他节点上占用的开销更少。 例如，如果你有 5 个升级域，每个升级域中的节点要处理大约 20% 的流量。 如果需要关闭升级域进行升级，则需要将负载转移到某个位置。 增加升级域意味着可以减少需要在群集中的其他节点上保留的开销。

使用许多升级域的缺点是升级花费的时间往往更长。 完成升级域之后，Service Fabric 会等待一小段时间再继续。 发生这种延迟的原因是为了显现并检测到升级造成的问题。 这种折衷是可接受的，因为可以防止错误的更改一次性影响过多的服务。

升级域太少会产生负面影响 – 当每个升级域关闭并进行升级时，大部分的整体容量将不可用。 例如，如果你只有 3 个升级域，则一次只能采用整体服务或群集容量的 1/3。 一次性关闭这么多的服务并非理想的结果，因为必须在其余的群集中拥有足够的容量才能处理工作负荷。 保留这种缓冲空间意味着在正常情况下，这些节点比增加服务运行成本时的负载更少。

环境中容错或升级域的总数没有实际限制，对于它们如何重叠也没有约束。 我们看到的通用结构是：

* 容错域与升级域之间采用 1:1 映射的关系
* 每个节点有一个升级域（物理或虚拟 OS 实例）
* “条带化”或“矩阵”模型，其中的容错域和升级域构成了沿对角线分布的计算机矩阵。

<center>
![故障域和升级域的布局][Image4]
</center>

至于要选择哪种分配并没有最佳答案，每种做法各有利弊。 例如，1FD:1UD 模型相当容易设置。 每个节点 1 个 UD 的模型是最类似于以前人们用于管理少量计算机的模型（其中每个计算机可独立关闭）。

最常见的模型（也是 Azure 中使用的模型）是 FD/UD 矩阵，其中 FD 和 UD 构成一个表，节点沿着对角线开始放置。 最终的结构是稀疏还是紧凑取决于相比于 FD 和 UD 数目的节点总数。 换而言之，对于明显较大的群集，几乎所有项目最终看起来就像是密集矩阵模式，如上图的右下角选项所示。

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容错域与升级域约束和最终行为
群集资源管理器将要在容错域与升级域之间保持服务的均衡视为约束。 可以在[此文](service-fabric-cluster-resource-manager-management-integration.md)中详细了解约束。 容错域和升级域约束的定义如下：“针对给定的服务分区，两个域之间的服务对象（无状态服务实例或有状态服务副本）数目差应该永远*不大于&1;*”。  这实际上意味着，对于给定的服务而言，特定的移动或排列方式可能无效，因为这会违反容错域和升级域的约束。

让我们看一个示例。 假设有一个&6; 节点群集，其中配置了&5; 个容错域和&5; 个升级域。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

假设要创建一个 TargetReplicaSetSize 为&5; 的服务。 副本驻留在 N1-N5 上。 事实上，N6 永远不被使用，不管创建的服务数目是多少。 为什么？ 让我们看看 N6 与当前布局之间的差异，以及选择 N6 时会发生情况。

下面是获得的布局，以及每个容错域和升级域的副本总数。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此布局在每个容错域与升级域的节点数目上是均衡的。 在每个容错域和升级域的副本数目上也一样是均衡的。 每个域都拥有相同数量的节点，以及相同数量的副本。

现在，让我们看看如果使用 N6 而不是 N2，会发生什么情况。 副本将如何分布？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

看到了差异了吗？ 此布局违反了容错域约束的定义。 FD0 有&2; 个副本，而 FD1 有&0; 个，FD0 与 FD1 之间的总差为&2;。 群集资源管理器不允许这种排列方式。 同样，如果选择 N2 和 N6（而不是 N1 和 N2），则会得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

尽管此布局从容错域的角度看是均衡的，但违反了升级域的约束（因为 UD0 有&0; 个副本，而 UD1 有&2; 个）。 此布局也是无效的。

## <a name="configuring-fault-and-upgrade-domains"></a>配置容错域和升级域
定义容错域和升级域是在 Azure 托管的 Service Fabric 部署中自动完成的。 Service Fabric 从 Azure 中选择并使用环境信息。

如果你要创建自己的群集（或者要在开发环境中运行特定的拓扑），则需要自行提供容错域和升级域信息。 在本示例中，我们定义了一个&9; 节点本地开发群集，该群集跨&3; 个“数据中心”（每个数据中心有&3; 个机架）。 此群集还包含三个升级域，它们已在这三个数据中心之间条带化。 在群集清单模板中，该布局类似于：

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
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

通过 ClusterConfig.json 进行独立部署

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
> 在 Azure 部署中，由 Azure 分配容错域和升级域。 因此，Azure 基础结构选项中节点和角色的定义不包含容错域或升级域信息。
>
>

## <a name="placement-constraints-and-node-properties"></a>放置约束和节点属性
有时（事实上是大多数情况下），需要确保只在群集中的特定节点或一组特定节点上运行某些工作负荷。 例如，某些工作负荷可能需要 GPU 或 SSD，而有些则不用。 一个有说服力的示例就是，几乎在每个 n 层体系结构，都有专门的硬件来处理特定的工作负荷。 在这些体系结构中，特定的计算机充当应用程序的前端/接口服务端（因此通常在 Internet 上公开）。 不同的计算机集（通常包含不同的硬件资源）处理计算或存储层的工作（因此通常不会在 Internet 上公开）。 Service Fabric 甚至预期到，在微服务领域中有特定的工作负荷需要在特定硬件配置上运行，例如：

* 现有的 n 层应用程序已“提升并迁移”到 Service Fabric 环境
* 出于性能、缩放或安全性隔离原因，某个工作负荷需要在特定硬件上运行
* 出于策略或资源消耗原因，某个工作负荷应与其他工作负荷隔离

为了支持这种配置，Service Fabric 提供先进的标记概念，可将其应用到节点。 这些标记称为放置约束。 放置约束可用于指示应在何处运行特定服务。 约束集可扩展 - 可以使用任何键/值对。

<center>
![群集布局中的不同工作负荷][Image5]
</center>

节点上的不同键/值标记称为节点放置*属性*（或简称为节点属性）。 节点属性中指定的值可以是字符串、布尔值或带符号的长型值。 服务中的语句称为放置*约束*，因为它约束群集中可以运行服务的位置。 约束可以是针对群集中的不同节点属性运行的任何布尔值语句。 这些布尔值语句中的有效选择器包括：

1) 用于创建特定语句的条件检查

| 语句 | 语法 |
| --- |:---:|
| “等于” | "==" |
| “不等于” | "!=" |
| “大于” | ">" |
| “大于等于” | ">=" |
| “小于” | "<" |
| “小于等于” | "<=" |

2) 用于分组和逻辑运算的布尔值语句

| 语句 | 语法 |
| --- |:---:|
| “和” | "&&" |
| “或” | "&#124;&#124;" |
| “非” | "!" |
| “组合成单个语句” | "()" |

下面是基本约束语句的示例。

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

只有整个语句求值为“True”的节点才能放置服务。 未定义属性的节点不匹配包含该属性的任何放置约束。

Service Fabric 定义了一些默认节点属性，无需用户进行定义，系统即会自动使用这些属性。 截至本文发布时，在每个节点上定义的默认属性是 **NodeType** 和 **NodeName**。 因此举例而言，可以将放置约束编写为 `"(NodeType == NodeType03)"`。 通常，我们认为 NodeType 是最常用的属性之一。 它是一个有用的属性，因为它与计算机的类型之间存在一一对应关系，而这又相当于与传统 n 层应用程序体系结构的工作负荷类型发生一一对应关系。

<center>
![放置约束和节点属性][Image6]
</center>

假设为给定节点类型定义了以下节点属性：

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集。 在 Azure Resource Manager 模板中，节点类型名称等群集元素可能已参数化，类似于 "[parameters('vmNodeType1Name')]" 而不是 "NodeType01"。

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

可以针对服务创建服务放置*约束*，如下所示：

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

如果确定 NodeType01 的所有节点都有效，则也可以选择该节点类型。

服务放置约束的一个突出优点是它们可以在运行时动态更新。 因此如果需要，你可以在群集中移动服务、添加和删除要求，等等。Service Fabric 负责确保服务保持运行且可用，即使此类更改持续进行。

C#：

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell：

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

放置约束（以及即将讨论的许多其他协调器控制）是针对每个不同的命名服务实例指定的。 更新始终会取代（覆盖）以前指定的值。

节点上的属性目前是通过群集定义来定义的，因此在不升级群集的情况下无法更新。 需要先将每个受影响的节点关闭再启动才能升级节点的属性。

## <a name="capacity"></a>容量
任何协调器的最重要作业之一是帮助管理群集中的资源消耗。 尝试有效运行服务时，最不想遇到的情况是许多节点处于热状态，而其他节点处于冷状态。 热节点会导致资源争用和性能不佳，而冷节点意味着资源浪费/成本增加。 在探讨均衡之前，让我们考虑一下，如果只是确保节点不在第一时间耗尽资源，情况会怎样？

Service Fabric 使用 `Metrics` 表示资源。 指标是你想要向 Service Fabric 描述的任何逻辑或物理资源。 指标的示例是诸如“WorkQueueDepth”或“MemoryInMb”的参数。 有关指标的配置及其用法的信息，请参阅[此文](service-fabric-cluster-resource-manager-metrics.md)

指标不同于放置约束和节点属性。 节点属性是节点本身的静态描述符，而指标与节点包含的资源，以及当服务在节点上运行时服务消耗的资源相关。 节点属性可以是“HasSSD”，可设置为 true 或 false。 该 SSD 上的可用空间量（和服务消耗的空间量）是类似于“DriveSpaceInMb”的指标。 节点上的“DriveSpaceInMb”容量设置为驱动器上的非保留总空间量。 服务将报告在运行时使用了多少指标。

请务必注意，像放置约束和节点属性一样，Service Fabric 群集资源管理器无法理解指标名称的含义。 指标名称只是一些字符串。 为避免混淆，最好是将单位声明为所创建的指标名称的一部分。

如果关闭所有的资源*均衡*功能，Service Fabric 群集资源管理器仍会尝试确保最终不会有任何节点超出其容量。 一般而言，除非群集的整体负载过于饱和，否则就可以实现此目的。 容量是群集资源管理器用来了解节点包含的资源量的另一个*约束*。 系统还会跟踪整个群集的剩余容量。 服务级别的容量和消耗量均以指标来表示。 因此举例而言，指标可能是“MemoryInMb”，给定的节点可能拥有 2048 个单位的“MemoryInMb”容量。 该节点上运行的某个服务可能目前正在消耗 64 个单位的“MemoryInMb”。

在运行时，群集资源管理器将跟踪每个节点上的每个资源有多少，以及剩余多少。 其评估方式是从节点的容量中减去该节点上运行的每个服务的所有声明使用量。 使用此信息，Service Fabric 群集资源管理器可找出要放置或移动副本的位置，使节点不会超过容量。

C#：

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell：

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```
<center>
![群集节点和容量][Image7]
</center>

可以查看群集清单中定义的容量：

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集。 在 Azure Resource Manager 模板中，节点类型名称等群集元素可能已参数化，类似于 "[parameters('vmNodeType2Name')]" 而不是 "NodeType02"。

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

此外，服务的负载也可能会动态变化（事实上这很常见）。 假设副本的负载从 64 更改为 1024，但是当时正在运行该副本的节点上只剩下 512（个单位的“MemoryInMb”指标）。 现在，放置副本或实例的位置可能失效，因为该节点上没有足够的空间。 如果该节点上所有副本和实例的合并使用量超出了该节点的容量，则可能会发生这种情况。 在任一情况下，群集资源管理器都必须介入，使节点上的容量消耗低于阈值。 为此，群集资源管理器会将该节点上的一个或多个副本或实例转移到其他节点。 移动副本时，群集资源管理器会尝试最大程度地减少这些移动操作造成的开销。 [此文](service-fabric-cluster-resource-manager-movement-cost.md)介绍了移动成本。

## <a name="cluster-capacity"></a>群集容量
那么，我们要如何防止整体群集太满？ 因为负载是动态的，群集资源管理器确实没有太多的办法。 服务可能有自身的负载高峰，群集资源管理器无法对其采取任何措施。 因此，今天群集中包含许多富空空间，但明天当用户成名之后，空间可能就会不足。 据说有些控件可以防止基本问题。 我们可做的第一件事是防止创建导致群集空间变满的新工作负荷。

假设你要创建一个无状态服务，它有某些关联的负载（比默认值还多且动态负载稍后才报告）。 我们假设该服务关注“DiskSpaceInMb”指标。 另外，假设该服务要针对每个服务实例消耗&5; 个单位的“DiskSpaceInMb”。 我们需要创建&3; 个服务实例。 很好！ 这意味着，群集中需要有 15 个单位的“DiskSpaceInMb”才能创建这些服务实例。 群集资源管理器将持续计算整体容量和每个指标的消耗量，因此我们可以轻松判断群集中是否有足够的空间。 如果没有足够的空间，群集资源管理器将拒绝创建服务调用。

由于要求仅是 15 个可用单位，此空间可以使用不同的方式分配。 例如，可在 15 个不同的节点上分配一个剩余单位的容量，或者在 5 个不同节点上分配 3 个剩余单位的容量。 只要群集资源管理器能够重新排列服务，在&3; 个节点上提供&5; 个单位，则最终就能放置好服务。 这种重新排列几乎始终可行，除非整个群集几乎已满，并且/或者所有服务都很“庞大”。

## <a name="buffered-capacity"></a>缓冲容量
群集资源管理器为了帮助用户管理整体群集容量而提供的另一项功能是，针对每个节点的指定容量添加一些保留缓冲区。 使用缓冲容量可以保留整体节点容量的某些部分，让它只用于在升级期间和发生节点故障时放置服务。 目前，缓冲区是通过群集定义针对所有节点的每个指标全局指定的。 为保留容量选择的值取决于群集中的容错域和升级域数目，以及所需的开销量。 如果容错域和升级域较多，则应该为缓冲容量选择较小的数值。 如果域数较多，在升级期间和发生故障时不可用的群集数很可能较少。 指定缓冲区百分比只有在同时指定了指标的节点容量时才有意义。

以下示例演示如何指定缓冲容量：

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
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

创建新服务会在群集耗尽指标的缓冲容量时失败。 这可以确保群集保留足够的备用开销，使升级和故障不会造成节点超过容量。 缓冲容量是可选功能，但建议在定义了指标容量的任何群集中使用。

群集资源管理器通过 PowerShell 和查询 API 公开此信息。 这样，用户即可查看缓冲容量设置、总容量以及群集中使用的每个指标的当前消耗量。 下面提供了该输出的示例：

```posh
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
* 有关群集资源管理器中的体系结构和信息流的信息，请参阅[此文](service-fabric-cluster-resource-manager-architecture.md)
* 定义重整指标是合并（而不是分散）节点上负载的一种方式。 若要了解如何配置重整，请参阅[此文](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 从头开始并[获取 Service Fabric 群集资源管理器简介](service-fabric-cluster-resource-manager-introduction.md)
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

