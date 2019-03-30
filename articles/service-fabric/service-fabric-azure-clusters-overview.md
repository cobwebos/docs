---
title: 在 Windows Server 和 Linux 上创建 Azure Service Fabric 群集 | Microsoft 文档
description: Service Fabric 群集会在 Windows Server 或 Linux 上运行，这意味着可以在能够运行 Windows Server 和 Linux 的任何位置部署和承载 Service Fabric 应用程序。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: d1681aee9dc11f0dbd3133bced0b919a8c1623b8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670466"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Azure 上 Service Fabric 群集的概述
Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 群集中的计算机或 VM 称为群集节点。 群集可以扩展到数千个节点。 如果向群集添加新节点，Service Fabric 会在新增加的节点间重新平衡服务分区副本和实例。 应用程序总体性能提高，访问内存的争用减少。 如果没有高效使用群集中的节点，可以减少群集中节点的数量。 Service Fabric 会再次在减少的节点间重新平衡分区副本和实例以更加充分利用每个节点上的硬件。

节点类型定义群集中一组节点（虚拟机）的大小、数量和属性。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 节点类型用于定义一组群集节点（如“前端”或“后端”）的角色。 群集可以有多个节点类型，但主节点类型必须至少有 5 个 VM 供群集用于生产（或至少有 3 个 VM 用于测试群集）。 [Service Fabric 系统服务](service-fabric-technical-overview.md#system-services)位于主节点类型的节点上。 

## <a name="cluster-components-and-resources"></a>群集组件和资源
Azure 上的 Service Fabric 群集是一种 Azure 资源，它使用其他 Azure 资源并与之交互：
* VM 和虚拟网卡
* 虚拟机规模集
* 虚拟网络
* 负载均衡器
* 存储帐户
* 公共 IP 地址

![Service Fabric 群集][Image]

### <a name="virtual-machine"></a>虚拟机
作为群集一部分的[虚拟机](/azure/virtual-machines/)称为节点，但从技术上讲，群集节点是指 Service Fabric 运行时进程。 需为每个节点分配节点名称（字符串）。 节点具有各种特征，如[放置属性](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。 每个计算机或 VM 都有一个自动启动服务 *FabricHost.exe*，此服务在引导时开始运行，并启动两个可执行文件 *Fabric.exe* 和 *FabricGateway.exe*，这两个可执行文件便构成了节点。 生产部署是每个物理或虚拟机一个节点。 在测试方案中，可以通过运行 *Fabric.exe* 和 *FabricGateway.exe* 的多个实例，在单台计算机或 VM 上托管多个节点。

每个 VM 均与虚拟网络接口卡 (NIC) 相关联，并且每个 NIC 均分配有一个专用 IP 地址。  VM 通过 NIC 分配给虚拟网络和本地均衡器。

群集中的所有 VM 都放置在虚拟网络中。  同一节点类型/规模集中的所有节点都放在虚拟网络上的同一子网上。  这些节点仅具有专用 IP 地址，并且无法在虚拟网络外直接寻址。  客户端可以通过 Azure 负载均衡器访问节点上的服务。

### <a name="scale-setnode-type"></a>规模集/节点类型
创建群集时，可以定义一个或多个节点类型。  节点类型中的节点或 VM 具有相同的大小和特征，例如 CPU 数、内存、磁盘数和磁盘 I/O。  例如，一个节点类型可能用于具有向 Internet 开放的端口的小型前端 VM，而另一个节点类型可能用于处理数据的大型后端 VM。 在 Azure 群集中，每个节点类型都映射到[虚拟机规模集](/azure/virtual-machine-scale-sets/)。

可使用规模集以集的形式部署和管理虚拟机集合。 Azure Service Fabric 群集中定义的每个节点类型均设置了独立的规模集。 Service Fabric 运行时使用 Azure VM 扩展启动到规模集中的每个虚拟机上。 可独立增加或减少每个节点类型、更改每个群集节点上运行的 OS SKU、打开不同的端口集，并使用不同的容量指标。 一个规模集包含五个[升级域](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains)和五个[容错域](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)，最多可包含 100 个 VM。  可以通过创建多个规模集/节点类型来创建超过 100 个节点的群集。

> [!IMPORTANT]
> 选择群集的节点类型数量以及每个节点类型的属性（大小、主节点、面向 Internet、VM 数量等）是一项重要任务。  有关详细信息，请阅读[群集容量规划注意事项](service-fabric-cluster-capacity.md)。

有关详细信息，请阅读 [Service Fabric 节点类型与虚拟机规模集](service-fabric-cluster-nodetypes.md)。

### <a name="azure-load-balancer"></a>Azure 负载均衡器
VM 实例在 [Azure 负载均衡器](/azure/load-balancer/load-balancer-overview)后面联接，该负载均衡器与[公共 IP 地址](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)和 DNS 标签相关联。  使用 *&lt;clustername&gt;* 预配群集时，DNS 名称 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* 便是与规模集前面的负载均衡器相关联的 DNS 标签。

群集中的 VM 只有[专用 IP 地址](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)。  管理流量和服务流量通过面向公众的负载均衡器进行路由。  网络流量通过 NAT 规则（客户端连接到特定节点/实例）或负载均衡规则（流量进入 VM 轮循机制）路由到这些计算机。  负载均衡器具有关联的公共 IP，其 DNS 名称的格式为：*&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com*。  公共 IP 是资源组中的另一个 Azure 资源。  如果在群集中定义多个节点类型，则会为每个节点类型/规模集创建一个负载均衡器。 或者，可以为多个节点类型设置单个负载均衡器。  主节点类型具有 DNS 标签 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com*，其他节点类型具有 DNS 标签 *&lt;clustername&gt;-&lt;nodetype&gt;.&lt;location&gt;.cloudapp.azure.com*。

### <a name="storage-accounts"></a>存储帐户
每个群集节点类型均受 [Azure 存储帐户](/azure/storage/common/storage-introduction)和托管磁盘的支持。

## <a name="cluster-security"></a>群集安全性
Service Fabric 群集是你拥有的资源。  你应保护群集，防止未经授权的用户与其连接。 当在群集上运行生产工作负荷时，安全的群集环境尤为重要。 

### <a name="node-to-node-security"></a>节点到节点安全性
节点到节点安全性可保护群集中 VM 或计算机之间的通信。 这种安全性方案确保只有已获授权加入群集的计算机可以参与到托管群集中的应用程序和服务。 Service Fabric 使用 X.509 证书保护群集，提供应用程序安全功能。  需要使用群集证书来保护群集流量并提供群集和服务器身份验证。  自签名证书可用于测试群集，但在保护生产群集时应使用来自受信任证书颁发机构的证书。

有关详细信息，请阅读[节点到节点安全性](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>客户端到节点安全性
客户端到节点安全性对客户端进行身份验证，并保护客户端与群集中单个节点之间的通信。 这种类型的安全性确保只有已获授权的用户可以访问群集与群集上部署的应用程序。 客户端通过其 X.509 证书安全凭据进行唯一标识。 可以使用任意数量的可选客户端证书向群集验证管理员或用户客户端的身份。

除客户端证书外，还可以将 Azure Active Directory 配置为向群集验证客户端身份。

有关详细信息，请阅读[客户端到节点安全性](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>基于角色的访问控制
基于角色的访问控制 (RBAC) 允许对 Azure 资源分配细粒度的访问控制。  你可以为订阅、资源组和资源分配不同的访问规则。  除非在较低级别被覆盖，否则将沿资源层次结构继承 RBAC 规则。  可以使用 RBAC 规则在 AAD 上分配任何用户或用户组，以便指定的用户和组可以修改你的群集。  有关详细信息，请阅读 [Azure RBAC 概述](/azure/role-based-access-control/overview)。

Service Fabric 还支持使用访问控制限制对不同用户组的某些群集操作的访问。 这就使得群集更加安全。 连接到群集的客户端支持两种访问控制类型：管理员角色和用户角色。  

有关详细信息，请阅读 [Service Fabric 基于角色的访问控制 (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac)。

### <a name="network-security-groups"></a>网络安全组 
网络安全组 (NSG) 控制子网、VM 或 特定 NIC 的入站和出站流量。  默认情况下，在将多个 VM 放在同一虚拟网络上时，它们可以通过任意端口相互通信。  如果要限制计算机之间的通信，可以将 NSG 定义为对网络进行分段或将 VM 彼此隔离。  如果群集中有多个节点类型，则可以将 NSG 应用于子网，以防止属于不同节点类型的计算机相互通信。  

有关详细信息，请阅读[安全组](/azure/virtual-network/security-overview)

## <a name="scaling"></a>扩展

应用程序的需求会不断变化。 可能需要增加群集资源来满足更多的应用程序工作负荷或网络流量，或者在需求下降时减少群集资源。 创建 Service Fabric 群集后，可以群集横向缩放（更改节点数）或纵向缩放（更改节点资源）该群集。 随时可以缩放群集，即使该群集上正在运行工作负荷。 在缩放群集的同时，应用程序也会随之自动缩放。

有关详细信息，请阅读[缩放 Azure 群集](service-fabric-cluster-scaling.md)。

## <a name="upgrading"></a>正在升级
Azure Service Fabric 群集是你拥有的，但部分由 Microsoft 管理的资源。 Microsoft 负责修补基础 OS 并在群集上执行 Service Fabric 运行时升级。 当 Microsoft 发布新版本时，可以将群集设置为接收自动运行时升级，或选择所需的受支持运行时版本。 除了运行时升级，还可以更新群集配置（例如证书或应用程序端口）。

有关详细信息，请阅读[升级群集](service-fabric-cluster-upgrade.md)。

## <a name="supported-operating-systems"></a>支持的操作系统
可以在运行以下操作系统的虚拟机上创建群集：

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4（预览支持）

> [!NOTE]
> 如果决定要在 Windows Server 1709 上部署 Service Fabric，请注意，(1) 它不是长期服务分支，因此你可能必须在将来迁移版本 (2) 如果部署容器，基于 Windows Server 2016 构建的容器不适用于 Windows Server 1709，反之亦然（你必须重新生成它们才能对其进行部署）。
>


## <a name="next-steps"></a>后续步骤
详细了解如何[保护](service-fabric-cluster-security.md)、[缩放](service-fabric-cluster-scaling.md)和[升级](service-fabric-cluster-upgrade.md) Azure 群集。

了解 [Service Fabric 支持选项](service-fabric-support.md)。

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG