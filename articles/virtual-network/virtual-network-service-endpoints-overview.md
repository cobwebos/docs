---
title: Azure 虚拟网络服务终结点 | Microsoft Docs
description: 了解如何使用服务终结点通过虚拟网络直接访问 Azure 资源。
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2018
ms.author: anithaa
ms.custom: ''
ms.openlocfilehash: dbcb1d87fafe085d6232fa621fbd9e211fa4174d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="virtual-network-service-endpoints"></a>虚拟网络服务终结点

虚拟网络 (VNet) 服务终结点可通过直接连接将 VNet 的虚拟网络专用地址空间和标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。

此功能针对以下 Azure 服务和区域提供：

- **Azure 存储**：已正式发布。 Azure 公有云和 Azure 政府中的所有区域。
- **Azure SQL 数据库**：在所有 Azure 区域正式发布。 
- **Azure SQL 数据仓库**：预览版。 Azure 公有云中的所有区域。

有关预览版的最新通知，请查看 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)页。

>[!NOTE]
> 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="key-benefits"></a>主要优点

服务终结点提供以下优势：

- **提高 Azure 服务资源的安全性**：使用服务终结点，可在虚拟网络中保护 Azure 服务资源。 在虚拟网络中保护服务资源可以完全消除通过公共 Internet 对这些资源进行访问，只允许来自客户自己的虚拟网络的流量，从而提高了安全性。
- **来自虚拟网络的 Azure 服务流量的最佳路由**：当前，强制 Internet 流量发往本地和/或虚拟设备（称为强制隧道）的虚拟网络中的任何路由也会强制 Azure 服务流量采用与 Internet 流量相同的路由。 服务终结点为 Azure 流量提供最佳路由。 

  终结点始终将直接来自虚拟网络的服务流量转发到 Microsoft Azure 主干网络上的服务。 将流量保留在 Azure 主干网络上可以通过强制隧道持续审核和监视来自虚拟网络的出站 Internet 流量，而不会影响服务流量。 详细了解[用户定义的路由和强制隧道](virtual-networks-udr-overview.md)。
- **设置简单，管理开销更少**：不再需要使用虚拟网络中的保留公共 IP 地址通过 IP 防火墙保护 Azure 资源。 无需使用 NAT 或网关设备即可设置服务终结点。 只需单击一下子网，即可配置服务终结点。 不会产生与终结点维护相关的额外开销。

## <a name="limitations"></a>限制

- 该功能仅适用于使用 Azure 资源管理器部署模型部署的虚拟网络。
- 终结点在 Azure 虚拟网络中配置的子网上启用。 终结点不可用于从本地发往 Azure 服务的流量。 有关详细信息，请参阅[保护从本地进行的 Azure 服务访问](#securing-azure-services-to-virtual-networks)
- 服务终结点仅适用于虚拟网络区域中的 Azure 服务流量。 为了支持 Azure 存储的 RA-GRS 和 GRS 流量，终结点还经过扩展，包括虚拟网络所部署到的配对区域。 详细了解 [Azure 配对区域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)。

## <a name="securing-azure-services-to-virtual-networks"></a>在虚拟网络中保护 Azure 服务

- 虚拟网络服务终结点为 Azure 服务提供虚拟网络的标识。 在虚拟网络中启用服务终结点后，可以通过将虚拟网络规则添加到资源，在虚拟网络中保护 Azure 服务资源。
- 当前，来自虚拟网络的 Azure 服务流量使用公共 IP 地址作为源 IP 地址。 使用服务终结点时，服务流量会在通过虚拟网络访问 Azure 服务时改用虚拟网络专用地址作为源 IP 地址。 通过这种切换，无需 IP 防火墙中使用的保留公共 IP 地址即可访问服务。
- __保护从本地进行的 Azure 服务访问__：

  默认情况下，无法从本地网络访问在虚拟网络中保护的 Azure 服务资源。 若要允许来自本地的流量，还必须允许来自本地或 ExpressRoute 的公共（通常为 NAT）IP 地址。 可通过 Azure 服务资源的 IP 防火墙配置添加这些 IP 地址。

  ExpressRoute：如果是在本地使用 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，则在进行公共对等互连或 Microsoft 对等互连时，需标识所用的 NAT IP 地址。 进行公共对等互连时，每条 ExpressRoute 线路默认情况下会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。 进行 Microsoft 对等互连时，所用 NAT IP 地址由客户或服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 详细了解[适用于 ExpressRoute 公共对等互连和 Microsoft 对等互连的 NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

![在虚拟网络中保护 Azure 服务](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>配置

- 服务终结点在虚拟网络中的子网上配置。 终结点可以处理该子网中运行的任何类型的计算实例。
- 可以针对子网中的所有受支持 Azure 服务（例如 Azure 存储或 Azure SQL 数据库）配置多个服务终结点。
- 虚拟网络应与 Azure 服务资源位于同一区域。 如果使用 GRS 和 RA-GRS Azure 存储帐户，则主帐户必须与虚拟网络位于同一区域。
- 配置了终结点的虚拟网络可与 Azure 服务资源位于相同或不同的订阅中。 有关设置终结点和保护 Azure 服务时所需的权限的详细信息，请参阅[预配](#Provisioning)。
- 对于受支持的服务，可以使用服务终结点在虚拟网络中保护新的或现有的资源。

### <a name="considerations"></a>注意事项

- 启用服务终结点后，与子网中的服务通信时，该子网中虚拟机的源 IP 地址将从公共 IPv4 地址改为专用 IPv4 地址。 在进行这种切换的过程中，与服务建立的所有现有打开的 TCP 连接将会关闭。 针对子网的服务启用或禁用服务终结点时，请确保未运行任何关键任务。 此外，请确保在完成 IP 地址切换后，应用程序可以自动连接到 Azure 服务。

  IP 地址切换只会影响自己的虚拟网络发出的服务流量， 而不会影响到发往或发自分配给虚拟机的公共 IPv4 地址的其他任何流量。 对于 Azure 服务，如果现有的防火墙规则使用 Azure 公共 IP 地址，这些规则会阻止切换到虚拟网络专用地址。
- 使用服务终结点时，Azure 服务的 DNS 条目会保持不变，继续解析为分配给 Azure 服务的公共 IP 地址。
- 使用服务终结点的网络安全组 (NSG)：
  - 默认情况下，NSG 允许出站 Internet 流量，因此，也允许来自 VNet 的流量发往 Azure 服务。 使用服务终结点时，处理方式仍旧如此。 
  - 如果想要拒绝所有出站 Internet 流量并只允许发往特定 Azure 服务的流量，可以在 NSG 中使用“Azure 服务标记”。 可以在 NSG 规则中将受支持的 Azure 服务指定为目标，每个标记下面的 IP 地址的维护由 Azure 提供。 有关详细信息，请参阅 [NSG 的 Azure 服务标记](https://aka.ms/servicetags)。 

### <a name="scenarios"></a>方案

- **对等互连的、连接的或多个虚拟网络**：若要在一个虚拟网络中的多个子网内或者跨多个虚拟网络保护 Azure 服务，可以针对每个子网单独启用服务终结点，在所有子网中保护 Azure 服务资源。
- **筛选从虚拟网络发往 Azure 服务的出站流量**：如果想要检查或筛选从虚拟网络发往 Azure 服务的流量，可在该虚拟网络中部署网络虚拟设备。 然后，可将服务终结点应用到部署了网络虚拟设备的子网，只在该子网中保护 Azure 服务资源。 如果希望使用网络虚拟设备筛选将从虚拟网络发起的 Azure 服务访问限制为特定的 Azure 资源，此方案可能很有帮助。 有关详细信息，请阅读[网络虚拟设备出口](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
- **在直接部署到虚拟网络的服务中保护 Azure 资源**：可将各种 Azure 服务直接部署到虚拟网络中的特定子网。 可以通过在托管服务子网上设置服务终结点，在[托管服务](virtual-network-for-azure-services.md)子网中保护 Azure 服务资源。

### <a name="logging-and-troubleshooting"></a>日志记录和故障排除

为特定的服务配置服务终结点后，请通过以下方式验证服务终结点路由是否生效： 
 
- 验证服务诊断中任何服务请求的源 IP 地址。 使用服务终结点的所有新请求会将请求的源 IP 地址显示为分配给从虚拟网络发出请求的客户端的虚拟网络专用地址。 如果不使用终结点，此地址是 Azure 公共 IP 地址。
- 查看子网中任何网络接口上的有效路由。 服务的路由：
  - 显示更具体的默认路由用于寻址每个服务的前缀范围
  - nextHopType 为 *VirtualNetworkServiceEndpoint*
  - 指示与任何强制隧道路由相比，它还与服务另外建立了一个有效的直接连接

>[!NOTE]
> 服务终结点路由会替代与 Azure 服务匹配的地址前缀的 BGP 或 UDR 路由。 详细了解如何[排查有效路由问题](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>设置

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。 若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有 *Microsoft.Network/JoinServicetoaSubnet* 权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)以及将特定的权限分配到[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

虚拟网络和 Azure 服务资源可以位于相同或不同的订阅中。 如果虚拟网络和 Azure 服务资源位于不同的订阅中，资源必须在相同的 Active Directory (AD) 租户下。 

## <a name="pricing-and-limits"></a>定价和限制

使用服务终结点不会产生额外的费用。 Azure 服务（Azure 存储和 Azure SQL 数据库）的当前定价模型保持不变。

虚拟网络中的服务终结点总数没有限制。

对于 Azure 服务资源（例如 Azure 存储帐户），服务可能会对用于保护资源的子网数目施加限制。 有关详细信息，请参阅[后续步骤](#next-steps)中所述的各种服务的文档。

## <a name="next-steps"></a>后续步骤

- 了解如何[配置虚拟网络服务终结点](tutorial-restrict-network-access-to-resources.md)
- 了解如何[在虚拟网络中保护 Azure 存储帐户](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- 了解如何[在虚拟网络中保护 Azure SQL 数据库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- 了解[虚拟网络中的 Azure 服务集成](virtual-network-for-azure-services.md)
-  快速入门：[Azure 资源管理器模板](https://azure.microsoft.com/en-us/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)，用于在 VNet 的子网上设置服务终结点，并保护访问该子网的 Azure 存储帐户。

