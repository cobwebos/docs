---
title: "Azure 虚拟网络服务终结点 | Microsoft Docs"
description: "了解如何使用服务终结点通过虚拟网络直接访问 Azure 资源。"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 297c51cca31d981ffadd20cccce1cd3dd69dc81c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-service-endpoints-preview"></a>虚拟网络服务终结点（预览）

虚拟网络 (VNet) 服务终结点可通过直接连接将 VNet 的虚拟网络专用地址空间和标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。

此功能以__预览版__的形式针对以下 Azure 服务和区域提供：

__Azure 存储__：WestCentralUS、WestUS2、EastUS、WestUS、AustraliaEast、AustraliaSouthEast。

__Azure SQL 数据库__：WestCentralUS、WestUS2、EastUS。

有关预览版的最新通知，请查看 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)页。

>[!NOTE]
在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="key-benefits"></a>主要优势

服务终结点提供以下优势：

- __提高 Azure 服务资源的安全性__

  使用服务终结点，可在虚拟网络中保护 Azure 服务资源。 这就提高了安全性，完全消除了通过公共 Internet 对这些资源进行访问，只允许来自客户自己的虚拟网络的流量。

- __来自虚拟网络的 Azure 服务流量的最佳路由__

  当前，VNet 中强制 Internet 流量通过本地和/或虚拟设备（称为强制隧道）的任何路由也会强制 Azure 服务流量采用与 Internet 流量相同的路由。 服务终结点为 Azure 流量提供最佳路由。 

  终结点始终将直接来自 VNet 的服务流量转发到 Microsoft Azure 主干网络上的服务。 这样，便可以通过强制隧道持续审核和监视来自 VNet 的出站 Internet 流量，而不会影响服务流量。 详细了解[用户定义的路由和强制隧道](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

- __设置简单，管理开销更少__

  不再需要使用 VNet 中的保留公共 IP 地址通过 IP 防火墙保护 Azure 资源。 无需使用 NAT 或网关设备即可设置服务终结点。 只需单击一下子网，即可配置服务终结点。 不会产生与终结点维护相关的额外开销。

## <a name="limitations"></a>限制

- 该功能仅适用于使用 Azure 资源管理器部署模型部署的 VNet。
- 终结点在 Azure VNet 中配置的子网上启用。 终结点不可用于从本地发往 Azure 服务的流量。 有关更多详细信息，请参阅[保护从本地进行的 Azure 服务访问](#securing-azure-services-to-virtual-networks)
- 服务终结点仅适用于 VNet 区域中的 Azure 服务流量。 为了支持 Azure 存储的 RA-GRS 和 GRS 流量，该功能还经过扩展，包括 VNet 所部署到的配对区域。 详细了解 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)。

## <a name="securing-azure-services-to-virtual-networks"></a>在虚拟网络中保护 Azure 服务

- VNet 服务终结点为 Azure 服务提供 VNet 的标识。 在 VNet 中启用服务终结点后，可以通过将虚拟网络规则添加到资源，在 VNet 中保护 Azure 服务资源。
- 当前，来自 VNet 的 Azure 服务流量使用公共 IP 作为源 IP。 使用服务终结点时，服务流量会在通过 VNet 访问 Azure 服务时改用 VNet 专用地址作为源 IP。 通过这种切换，无需 IP 防火墙中使用的保留公共 IP 地址即可访问服务。
- __保护从本地进行的 Azure 服务访问__：
 
   默认情况下，无法从本地网络访问在虚拟网络中保护的 Azure 服务资源。 如果想要允许来自本地的流量，还必须允许来自本地或 ExpressRoute 线路的 NAT IP 地址。 可通过 Azure 服务资源的 IP 防火墙配置添加 NAT IP 地址。
  
   __ExpressRoute__： 

    如果从本地使用 [ExpressRoute](/azure/expressroute/expressroute-introduction) (ER)，每条 Expressroute 线路会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。  若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这两个 IP 地址。  若要查找 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

![在虚拟网络中保护 Azure 服务](media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>配置

- 服务终结点在 VNet 中的子网上配置。 终结点可以处理该子网中运行的任何类型的计算实例。
- 对于子网中的某个特定服务，只能启用一个服务终结点。 可以针对子网中的所有受支持 Azure 服务（例如存储和 SQL）配置多个服务终结点。
- VNet 应与 Azure 服务资源位于同一区域。 对于 Azure 存储，如果使用 GRS 和 RA-GRS 帐户，则主帐户应与 VNet 位于同一区域。
- 配置了终结点的 VNet 可与 Azure 服务资源位于相同或不同的订阅中。 有关设置终结点和保护 Azure 服务时所需的权限的详细信息，请参阅下面的[预配](#Provisioning)部分。
- 对于受支持的服务，可以使用服务终结点在 VNet 中保护新的或现有的资源。

### <a name="considerations"></a>注意事项

- 启用服务终结点后，与子网中的服务通信时，该子网中虚拟机的源 IP 地址将从公共 IPv4 地址改为专用 IPv4 地址。
  - 在进行这种切换的过程中，与服务建立的所有现有打开的 TCP 连接将会关闭。 针对子网的服务启用或禁用服务终结点时，请确保未运行任何关键任务。 此外，请确保在完成这种 IP 地址切换后，应用程序可以自动连接到 Azure 服务。
  - IP 地址切换只会影响自己的 VNet 发出的服务流量， 而不会影响到发往/发自分配给 VM 的公共 IPV4 地址的其他任何流量。
  - 对于 Azure 服务，如果现有的防火墙规则使用 Azure 公共 IP，这些规则会阻止切换到 VNet 专用地址。
- 使用服务终结点时，Azure 服务的 DNS 条目会保持不变，继续解析为分配给 Azure 服务的 IP 地址。
- 使用服务终结点的网络安全组 (NSG)：
  - 默认情况下，NSG 允许出站 Internet 流量，因此，也允许来自 VNet 的流量发往 Azure 服务。 使用服务终结点时，处理方式仍旧如此。
  - 如果想要拒绝所有出站 Internet 流量并只允许发往特定 Azure 服务的流量，可以在 NSG 中使用“Azure 服务标记”。 可以在 NSG 规则中将受支持的 Azure 服务指定为目标，每个标记下面的 IP 地址的维护由 Azure 提供。 有关详细信息，请参阅 [NSG 的 Azure 服务标记](https://aka.ms/servicetags)。

### <a name="scenarios"></a>方案
- 对等互连的、连接的或多个 VNet：

若要在一个 VNet 中的多个子网内或者跨多个 VNet 保护 Azure 服务，可以针对其中每个子网单独启用服务终结点，在所有这些子网中保护 Azure 服务资源。

- 筛选从 VNet 发往 Azure 服务的出站流量：

如果想要检查或筛选从虚拟网络发往 Azure 服务的流量，可在该 VNet 中部署网络虚拟设备 (NVA)。 然后，可将服务终结点应用到部署了 NVA 的子网，只在该子网中保护 Azure 服务资源。 如果希望使用 NVA 筛选将从 VNet 发起的 Azure 服务访问限制为特定的 Azure 资源，此方案可能很有帮助。 有关详细信息，请阅读 [NVA 入口](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas)一文。

- 在直接部署到 VNet 的服务中保护 Azure 资源：

可将各种 Azure 服务直接部署到 VNet 中的特定子网。 可以通过在托管服务子网上设置服务终结点，在[托管服务](virtual-network-for-azure-services.md)子网中保护 Azure 服务资源。

### <a name="logging-and-troubleshooting"></a>日志记录和故障排除

为特定的服务配置服务终结点后，请通过以下方式验证服务终结点路由是否生效： 

- __监视 Azure 服务诊断__：可以通过验证服务诊断中的任何服务请求的“源 IP”，确认请求访问是否来自专用网络（即你的 VNet）。 使用服务终结点的所有新请求会将请求的“源 IP”显示为分配给从 VNet 发出请求的客户端的 VNet 专用地址。 如果不使用终结点，此地址将是 Azure 公共 IP。

- 子网中的任何 NIC 上的“有效路由”显示更具体的“默认”路由用于寻址该服务的地址前缀范围。 路由的 nextHopType 为“VirtualNetworkServiceEndpoint”。 此路由指示与任何强制隧道路由相比，它还与服务另外建立了一个有效的直接连接。

>[!NOTE]
与 Azure 服务一样，服务终结点路由会替代用于地址前缀匹配的 BGP 或 UDR 路由。 详细了解如何[排查有效路由问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>设置

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。

若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/JoinServicetoaSubnet”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)以及将特定的权限分配到[自定义角色](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)。

VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果 VNet 和 Azure 服务资源位于不同的订阅中，在预览期，资源应在相同的 Active Directory (AD) 租户下。 

## <a name="pricing-and-limits"></a>定价和限制

使用服务终结点不会产生额外的费用。 Azure 服务（Azure 存储和 Azure SQL 数据库）的当前定价模型保持不变。

VNet 中的服务终结点总数没有限制。

对于 Azure 服务资源（例如存储帐户），服务可能会对用于保护资源的子网数目施加限制。 有关详细信息，请参阅[后续步骤](#next%20steps)中所述的各种服务的文档。

## <a name="next-steps"></a>后续步骤

- 了解[如何配置 VNet 服务终结点](virtual-network-service-endpoints-configure.md)
- 详细了解[在虚拟网络中保护 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- 详细了解[在虚拟网络中保护 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
- [虚拟网络的 Azure 服务集成概述](virtual-network-for-azure-services.md)


