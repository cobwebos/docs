---
title: 计划 Azure 虚拟网络 | Microsoft Docs
description: 了解如何基于隔离、连接性和位置需求来计划虚拟网络。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: jdial
ms.openlocfilehash: fd290420c2c755e07f6949750e3a88bcb64682f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656901"
---
# <a name="plan-virtual-networks"></a>计划虚拟网络

创建用于试验的虚拟网络非常简单，但却可能是，会在一段时间内部署多个虚拟网络以支持组织的生成需要。 通过进行一些规划，能够更有效地部署虚拟网络和连接所需的资源。 如果已熟悉虚拟网络并具有一定的使用经验，本文中的信息将非常有用。 如果不熟悉虚拟网络，建议阅读[虚拟网络概述](virtual-networks-overview.md)。

## <a name="naming"></a>命名

所有 Azure 资源都有名称。 该名称在一个范围内必须是唯一的，但每种资源类型可能会有所不同。 例如，虚拟网络的名称在[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)内必须是唯一的，但可在[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)或 Azure [区域](https://azure.microsoft.com/regions/#services)内重复。 当一段时间内管理多个网络资源时，定义命名资源时可一致使用的命名约定很有用。 有关建议，请参阅[命名约定](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking)。

## <a name="regions"></a>区域

在 Azure 区域和订阅中创建所有 Azure 资源。 只能在与资源位于相同区域和订阅的虚拟网络中创建资源。 但是，可连接存在于不同订阅和区域中的虚拟网络。 有关详细信息，请参阅[连接性](#connectivity)。 在决定要在哪个区域部署资源时，请考虑资源的使用者位于何处：

- 资源的使用者通常希望其资源的网络延迟最低。 若要确定指定位置与 Azure 区域之间的相对延迟，请参阅[查看相对延迟](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 是否有数据驻留、主权、符合性或复原能力需求？ 如果有，选择符合需求的区域至关重要。 有关详细信息，请参阅[Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)。
- 是否需要在部署资源的相同 Azure 区域内跨 Azure 可用性区域提供复原能力？ 可将资源（如虚拟机 (VM)）部署到相同虚拟网络中的不同可用性区域。 但并非所有 Azure 区域都支持可用性区域。 若要详细了解可用性区域和支持它们的区域，请参阅[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="subscriptions"></a>订阅

可根据需要在每个订阅中部署尽可能多的虚拟网络，直至达到[上限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。 例如，一些组织的不同部门有不同的订阅。 有关订阅的详细信息和注意事项，请参阅[订阅监管](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy)。

## <a name="segmentation"></a>分段

可为每个订阅和每个区域创建多个虚拟网络。 可在每个虚拟网络中创建多个子网。 以下注意事项可帮助确定需要多少个虚拟网络和子网：

### <a name="virtual-networks"></a>虚拟网络

虚拟网络是 Azure 公共网络的虚拟隔离部分。 每个虚拟网络专用于你的订阅。 决定在订阅中创建一个虚拟网络还是多个虚拟网络时需要考虑的事项：

- 是否存在将流量隔离到单独的虚拟网络中的组织安全需求？ 可选择连接或不连接虚拟网络。 如果连接虚拟网络，则可实施网络虚拟设备（如防火墙）来控制虚拟网络之间的流量流。 有关详细信息，请参阅[安全性](#security)和[连接性](#connectivity)。
- 是否存在将虚拟网络隔离到单独的[订阅](#subscriptions)或[区域](#regions)的组织需求？
- [网络接口](virtual-network-network-interface.md)使 VM 能够与其他资源进行通信。 可为每个网络接口分配一个或多个专用 IP 地址。 虚拟网络中需要多少个网络接口和[专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)？ 在虚拟网络中可以拥有的网络接口和专用 IP 地址数有[上限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。
- 是否要将虚拟网络连接到其他虚拟网络或本地网络？ 可选择将某些虚拟网络互相连接或连接到本地网络，而不是其他网络。 有关详细信息，请参阅[连接性](#connectivity)。 连接到另一个虚拟网络或本地网络的每个虚拟网络必须具有唯一的地址空间。 每个虚拟网络都向其地址空间分配了一个或多个公共和专用地址范围。 地址范围以无类别的 Internet 域路由 (CIDR) 格式指定，例如 10.0.0.0/16。 详细了解虚拟网络的[地址范围](manage-virtual-network.md#add-or-remove-an-address-range)。
- 是否对不同虚拟网络中的资源有任何组织管理需求？ 如果有，可将资源分隔到单独的虚拟网络中，以简化组织中个人的[权限分配](#permissions)，或将不同的[策略](#policies)分配给不同的虚拟网络。
- 将某些 Azure 服务资源部署到虚拟网络时，他们将创建自己的虚拟网络。 若要确定 Azure 服务是否创建自己的虚拟网络，请参阅每个[可部署到虚拟网络中的 Azure 服务](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)的信息。

### <a name="subnets"></a>子网

可将虚拟网络分段为一个或多个子网，直至达到[上限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)。 决定在订阅中创建一个子网还是多个虚拟网络时需要考虑的事项：

- 在虚拟网络的地址空间内，每个子网必须具有唯一的地址范围，且以 CIDR 格式指定。 其地址范围不能与虚拟网络中其他子网重叠。
- 如果计划将某些 Azure 服务资源部署到虚拟网络中，则他们可能需要或创建自己的子网，因此必须有足够的未分配空间才能进行此操作。 若要确定 Azure 服务是否创建自己的子网，请参阅每个[可部署到虚拟网络中的 Azure 服务](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)的信息。 例如，如果使用 Azure VPN 网关将虚拟网络连接到本地网络，虚拟网络必须具有该网关的专用子网。 详细了解[网关子网](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。
- 默认情况下，Azure 在虚拟网络中的所有子网之间路由流量。 例如，可替代 Azure 的默认路由以防止在子网之间进行 Azure 路由，或通过网络虚拟设备在子网之间路由流量。 如果要求相同虚拟网络中资源之间的流量流经网络虚拟设备 (NVA)，请将资源部署到不同的子网。 有关详细信息，请参阅[安全性](#security)。
- 可将对 Azure 资源（例如 Azure 存储帐户或 Azure SQL 数据库）的访问权限限制为具有虚拟网络服务终结点的特定子网。 此外，可拒绝通过 Internet 访问资源。 可创建多个子网，并为某些子网启用服务终结点，但不启用其他项。 详细了解[服务终结点](virtual-network-service-endpoints-overview.md)，以及可为其启用的 Azure 资源。
- 可将零个或一个网络安全组与虚拟网络中的每个子网相关联。 可将相同或不同的网络安全组关联到每个子网。 每个网络安全组都包含规则，允许或拒绝到达和来自源和目标的流量。 详细了解[网络安全组](#traffic-filtering)。

## <a name="security"></a>“安全”

可使用网络安全组和网络虚拟设备来筛选虚拟网络中到达或来自资源的流量。 可控制 Azure 如何路由来自子网的流量。 还可限制组织中的人员使用虚拟网络中的资源。

### <a name="traffic-filtering"></a>流量筛选

- 可使用网络安全组和/或筛选流量的 NVA 来筛选虚拟网络中资源之间的流量。 若要部署 NVA（如防火墙）来筛选流量，请参阅 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1)。 使用 NVA 时，还可以创建自定义路由将流量从子网路由到 NVA。 详细了解[流量路由](#traffic-routing)。
- 网络安全组包含多个默认安全规则，允许或拒绝到达或来自资源的流量。 可将网络安全组关联到网络接口和/或网络接口所在的子网。 若要简化安全规则的管理，建议尽可能将网络安全组与单独的子网相关联，而不是与子网内单独的网络接口相关联。
- 如果子网中不同 VM 需要应用不同的安全规则，则可将 VM 中的网络接口与一个或多个应用程序安全组相关联。 安全规则可以在其源和/或目标中指定应用程序安全组。 该规则仅适用于属于应用程序安全组成员的网络接口。 详细了解[网络安全组](security-overview.md)和[应用程序安全组](security-overview.md#application-security-groups)。
- Azure 在每个网络安全组中创建了多个默认安全规则。 其中一个默认规则允许所有流量在虚拟网络中的所有资源之间流动。 若要替代此行为，可使用网络安全组和/或自定义路由将流量路由到 NVA。 建议熟悉 Azure 的所有[默认安全规则](security-overview.md#default-security-rules)，并了解网络安全组规则如何应用于资源。

可查看使用 [NVA](/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 或[网络安全组](virtual-networks-dmz-nsg.md)在 Azure 和 Internet 之间实施 DMZ 的示例设计。

### <a name="traffic-routing"></a>流量路由

Azure 为子网中的出站流量创建多个默认路由。 可通过创建路由表并将其关联到子网来替代 Azure 的默认路由。 替代 Azure 的默认路由的常见原因是：
- 想要子网之间的流量流经 NVA。 了解如何[配置路由表以强制流量通过 NVA](tutorial-create-route-table-portal.md) 的详细信息。
- 想要通过 Azure VPN 网关强制所有 Internet 绑定流量通过 NVA 或本地。 强制 Internet 流量本地进行检查和记录通常被称为强制隧道。 详细了解如何配置[强制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)。

如果需要实施自定义路由，建议熟悉 [Azure 中的路由](virtual-networks-udr-overview.md)。

## <a name="connectivity"></a>连接

可使用虚拟网络对等将虚拟网络连接到其他虚拟网络，或使用 Azure VPN 网关将其连接到本地网络。

### <a name="peering"></a>对等互连

使用[虚拟网络对等](virtual-network-peering-overview.md)时，虚拟网络可位于相同的或不同的受支持 Azure 区域中。 虚拟网络可位于相同或不同的 Azure 订阅中，只要这两个订阅都分配给相同的 Azure Active Directory 租户即可。 建立对等之前，建议熟悉所有对等[需求和约束](virtual-network-manage-peering.md#requirements-and-constraints)。 相同区域中对等的虚拟网络中的资源之间的带宽与资源在同一虚拟网络中的带宽相同。

### <a name="vpn-gateway"></a>VPN 网关

可通过[站点到站点 VPN ](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或与 Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 的专用连接，使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)将虚拟网络连接到本地网络。

例如，可将对等和 VPN 网关结合使用以创建[中心辐射网络](/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)，其中分支虚拟网络连接到中心虚拟网络，并且中心虚拟网络连接到本地网络。

### <a name="name-resolution"></a>名称解析

虚拟网络中的资源无法使用 Azure 的[内置 DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)，解析对等虚拟网络中的资源名称。 若要在对等的虚拟网络中解析名称，请[部署自己的 DNS 服务器](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)或使用 Azure DNS [专用域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 在虚拟网络和本地网络的资源之间解析名称也需要部署自己的 DNS 服务器。

## <a name="permissions"></a>权限

Azure 对资源使用[基于角色的访问控制](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC)。 将权限分配到以下层次结构中的[范围](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope)：订阅、管理组、资源组和单独资源。 若要了解层次结构的详细信息，请参阅[组织资源](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure 虚拟网络及其所有相关功能（例如对等、网络安全组、服务终结点和路由表），可将组织的成员分配到内置[所有者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner)、[参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)或[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，然后将该角色分配到相应的范围。 如果要为虚拟网络功能的子集分配特定权限，请创建[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)并为该角色分配[虚拟网络](manage-virtual-network.md#permissions)、[子网和服务终结点](virtual-network-manage-subnet.md#permissions)、[网络接口](virtual-network-network-interface.md#permissions)[对等](virtual-network-manage-peering.md#permissions)、[网络和应用程序安全组](manage-network-security-group.md#permissions)或[路由表](manage-route-table.md#permissions)所需的特定权限。

## <a name="policy"></a>策略

通过 Azure 策略，可创建、分配和管理策略定义。 策略定义将在整个资源中强制实施不同的规则，以便资源符合组织标准和服务级别协议。 Azure 策略对资源进行评估，扫描并找到与所定义策略不相符的资源。 例如，可以定义和应用仅在特定资源组或区域中允许创建虚拟网络的策略。 另一个策略可能要求每个子网都有一个与之关联的网络安全组。 然后，在创建和更新资源时评估此策略。

策略应用于以下层次结构：订阅、管理组和资源组。 了解有关 [Azure 策略](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或部署某些虚拟网络[策略模板](policy-samples.md)示例的详细信息。

## <a name="next-steps"></a>后续步骤

了解[虚拟网络](manage-virtual-network.md)、[子网和服务终结点](virtual-network-manage-subnet.md)、[网络接口](virtual-network-network-interface.md)、[对等互连](virtual-network-manage-peering.md)、[网络和应用程序安全组](manage-network-security-group.md)或[路由表](manage-route-table.md)的所有任务、设置和选项。
