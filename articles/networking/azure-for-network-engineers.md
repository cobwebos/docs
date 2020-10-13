---
title: Azure ExpressRoute：面向网络工程师的 Azure
description: 本页向传统网络工程师介绍了 Azure 中的网络如何工作。
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 4f513da4e7883cd273098039c9c4a4645d849f0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515867"
---
# <a name="azure-for-network-engineers"></a>面向网络工程师的 Azure
作为传统网络工程师，你之前负责通过物理资产（例如路由器、交换机、电缆、防火墙）来构建基础设施。 在逻辑层上，你曾经配置过虚拟 LAN (VLAN)、跨树协议 (STP)、路由协议（RIP、OSPF、BGP）。 你曾经使用管理工具和 CLI 来管理你的网络。 云中的网络有所不同，其中的网络终结点是逻辑终结点，很少使用路由协议。 你将使用 Azure 资源管理器 API、Azure CLI 和 PowerShell 来配置和管理 Azure 中的资产。 你将首先了解 Azure 网络的基本租户，从而开始你在云中的网络探索旅程。 
## <a name="virtual-network"></a>虚拟网络
从下到上设计网络时，你需要收集一些基本信息。 该信息可能包括主机数、网络设备数、子网数、子网之间的路由、隔离域（例如 VLAN）。 此信息可用于确定网络和安全设备的规模，以及创建支持应用程序和服务的体系结构。

当你计划在 Azure 中部署应用程序和服务时，你首先在 Azure 中创建逻辑边界，这称为“虚拟网络”。 此虚拟网络与物理网络边界类似。 由于它是一个虚拟网络，因此你不需要物理设备，但仍需规划逻辑实体，例如 IP 地址、IP 子网、路由和策略。

当你在 Azure 中创建虚拟网络时，系统会为其预先配置一个 IP 范围 (10.0.0.0/16)。 此范围不是固定的，你可以定义自己的 IP 范围。 可以同时定义 IPv4 和 IPv6 地址范围。 为虚拟网络定义的 IP 范围不会播发到 Internet。 可以基于 IP 范围创建多个子网。 这些子网将用于向虚拟网络接口 (vNIC) 分配 IP 地址。 每个子网中的前四个 IP 地址都是保留地址，不能用于 IP 分配。 公有云中没有 VLAN 的概念。 但是，你可以根据所定义的子网在虚拟网络中形成隔离。

你可以创建一个包含所有虚拟网络地址空间的大型子网，也可以选择创建多个子网。 但是，如果你使用的是虚拟网关，Azure 会要求你创建一个名为“网关子网”的子网。 Azure 将使用此子网向虚拟网关分配 IP 地址。 

## <a name="ip-allocation"></a>IP 分配

将 IP 地址分配给主机时，实际上会将 IP 分配给网络接口卡 (NIC)。 可将两种类型的 IP 地址分配给 Azure 中的 NIC：

- 公共 IP 地址 - 用来与 Internet 以及未连接到虚拟网络的其他 Azure 资源进行入站和出站通信（不提供网络地址转换 (NAT)）。 向 NIC 分配公共 IP 地址是可选操作。 公共 IP 地址属于 Microsoft IP 地址空间。
- 专用 IP 地址 - 用于在虚拟网络、本地网络和 Internet 中通信（提供 NAT）。 即使配置公共 IP 地址空间，在虚拟网络中定义的 IP 地址空间也会被视为专用的空间。 Microsoft 不会将此空间播发到 Internet。 必须至少将一个专用 IP 地址分配给 VM。

就像使用物理主机或设备一样，可以通过两种方法（动态方法和静态方法）将 IP 地址分配给资源。 在 Azure 中，默认分配方法为动态方法，即在创建虚拟机 (VM) 或启动已停止的 VM 时分配 IP 地址。 停止或删除该 VM 时，会释放该 IP 地址。 要确保 VM 的 IP 地址保持不变，可将分配方法显式设置为静态。 在这种情况下，IP 地址是即时分配的。 只有在删除该 VM 或将其分配方法更改为动态时，才会释放该地址。 

专用 IP 地址是从虚拟网络中定义的子网中分配的。 对于 VM，请选择一个用于分配 IP 的子网。 如果某个 VM 包含多个 NIC，则可以为每个 NIC 选择不同的子网。

## <a name="routing"></a>路由
当你创建虚拟网络时，Azure 将为你的网络创建路由表。 此路由表包含以下类型的路由。
- 系统路由
- 子网默认路由
- 来自其他虚拟网络的路由
- BGP 路由
- 服务终结点路由
- 用户定义的路由 (UDR)

如果在未定义任何子网的情况下首次创建虚拟网络，Azure 将在路由表中创建路由条目。 这些路由称为系统路由。 系统路由是在此位置定义的。 你无法修改这些路由。 不过，你可以通过配置 UDR 来替代系统路由。

在虚拟网络中创建一个或多个子网时，Azure 会在路由表中创建默认条目，以便在虚拟网络中的这些子网之间通信。 可以使用静态路由（在 Azure 中是用户定义的路由 (UDR)）修改这些路由。

在两个虚拟网络之间创建虚拟网络对等互连时，会为每个虚拟网络（已为其创建对等互连）的地址空间中的每个地址范围添加一个路由。

如果本地网关与 Azure 虚拟网关交换了边界网关协议 (BGP) 路由，则会为每个从本地网关传播的路由添加一个路由。 这些路由在路由表中显示为 BGP 路由。

启用特定服务的服务终结点时，Azure 会将该服务的公共 IP 地址添加到路由表。 服务终结点是针对虚拟网络中的单个子网启用的。 启用某个服务终结点时，路由将仅添加到属于此服务的子网的路由表。 当地址更改时，Azure 自动管理路由表中的地址。

用户定义的路由也称为自定义路由。 可以在 Azure 中创建 UDR 来替代 Azure 的默认系统路由，或者向子网的路由表添加其他路由。 可以在 Azure 中创建一个路由表，然后将该路由表关联到虚拟网络子网。

当路由表中有竞争的条目时，Azure 会基于最长的前缀匹配来选择下一个跃点，这与传统路由器类似。 但是，如果存在多个具有相同地址前缀的下一跃点条目，Azure 将按以下顺序选择路由。
1. 用户定义的路由 (UDR)
1. BGP 路由
1. 系统路由

## <a name="security"></a>安全性

可使用网络安全组来筛选虚拟网络中到达或来自资源的网络流量。 还可以使用网络虚拟设备 (NVA)，例如 Azure 防火墙或其他供应商提供的防火墙。 可控制 Azure 如何路由来自子网的流量。 还可限制组织中的人员使用虚拟网络中的资源。

网络安全组 (NSG) 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网和/或 NIC 的网络流量。 NSG 可与子网或者已连接到子网的各个 NIC 相关联。 当 NSG 与某个子网相关联时，ACL 规则将应用到该子网中的所有 VM。 另外，可以通过将 NSG 直接关联到 NIC，对流向单个 NIC 的流量进行限制。

NSG 包含两种类型的规则：入站规则和出站规则。 在每组中，规则的优先级必须保持唯一。 每个规则包含以下属性：协议、源和目标端口范围、地址前缀、流量方向、优先级和访问类型。 所有 NSG 都包含一组默认规则。 默认规则无法删除，但由于给它们分配的优先级最低，可以用创建的规则来重写它们。

## <a name="verification"></a>验证
### <a name="routes-in-virtual-network"></a>虚拟网络中的路由
创建的路由、Azure 的默认路由，以及使用边界网关协议 (BGP) 通过 Azure VPN 网关（如果虚拟网络已连接到本地网络）从本地网络传播的任何路由的组合，就是子网中所有网络接口的有效路由。 可以使用门户、PowerShell 或 CLI 通过导航到 NIC 来查看这些生效的路由。
### <a name="network-security-groups"></a>网络安全组
应用到网络接口的有效安全规则是关联到网络接口以及网络接口所在子网的 NSG 的聚合。 可以使用门户、PowerShell 或 CLI 通过导航到 NIC 来查看 NSG 中对 VM 网络接口应用的所有生效的安全规则。

## <a name="next-steps"></a>后续步骤

了解[虚拟网络][VNet]。

了解[虚拟网络路由][vnet-routing]。

了解[网络安全组][network-security]。

<!--Link References-->
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[vnet-routing]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview
[network-security]: https://docs.microsoft.com/azure/virtual-network/security-overview

