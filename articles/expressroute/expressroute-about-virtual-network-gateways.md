---
title: 关于 ExpressRoute 虚拟网络网关 - Azure | Microsoft Docs
description: 了解 ExpressRoute 的虚拟网络网关。 本文包含有关网关 SKU 和类型的信息。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894380"
---
# <a name="about-expressroute-virtual-network-gateways"></a>关于 ExpressRoute 虚拟网络网关

若要通过 ExpressRoute 连接 Azure 虚拟网络和本地网络，必须先创建虚拟网络网关。 虚拟网络网关有两个用途： exchange IP 路由网络之间的路由和路由网络流量。 本文介绍了各 SKU 的网关类型、网关 Sku 和估计性能。 本文还介绍了 ExpressRoute [FastPath](#fastpath)，这是一项功能，可让你的本地网络中的网络流量绕过虚拟网络网关，从而提高性能。

## <a name="gateway-types"></a>网关类型

创建虚拟网络网关时，需要指定几项设置。 其中一个必要设置“-GatewayType”指定是否将网关用于 ExpressRoute 或 VPN 流量。 两种网关类型是：

* **Vpn** - 若要通过公共 Internet 发送加密流量，请使用网关类型“Vpn”。 这也称为 VPN 网关。 站点到站点连接、点到站点连接和 VNet 到 VNet 连接都使用 VPN 网关。

* **ExpressRoute** - 若要在专用连接上发送网络流量，请使用网关类型“ExpressRoute”。 这也称为 ExpressRoute 网关，是配置 ExpressRoute 时使用的网关类型。

对于每种网关类型，每个虚拟网络只能有一个虚拟网络网关。 例如，一个虚拟网络网关使用 -GatewayType Vpn，另一个使用 -GatewayType ExpressRoute。

## <a name="gwsku"></a>网关 SKU
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

如果要将网关升级为功能更强大的网关 SKU，在大多数情况下，可以使用 "AzVirtualNetworkGateway" PowerShell cmdlet。 此方法适用于升级到 Standard 和 HighPerformance SKU。 但是，若要升级到 UltraPerformance SKU，需要重新创建网关。 重新创建网关会导致停机。

### <a name="aggthroughput"></a>预估性能（按网关 SKU）
下表显示网关类型和估计性能。 此表适用于 Resource Manager 与经典部署模型。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> 应用程序性能取决于多种因素，例如端到端延迟和应用程序打开的流量数。 表中的数字表示应用程序在理想环境下理论上可达到的上限。
>
>

## <a name="gwsub"></a>网关子网

创建 ExpressRoute 网关之前，必须先创建网关子网。 网关子网包含虚拟网络网关 VM 和服务使用的 IP 地址。 创建虚拟网络网关时，会将网关 Vm 部署到网关子网，并配置所需的 ExpressRoute 网关设置。 不要将任何其他内容（例如，其他 Vm）部署到网关子网。 网关子网必须命名为“GatewaySubnet”才能正常工作。 将网关子网命名为“GatewaySubnet”，可以让 Azure 知道这就是要将虚拟网络网关 VM 和服务部署到的目标子网。

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

创建网关子网时，请指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 

规划网关子网大小时，请参阅你计划创建的配置的相关文档。 例如，ExpressRoute/VPN 网关共存配置所需的网关子网比大多数其他配置要大。 此外，可能需要确保网关子网包含足够多的 IP 地址，以便应对将来可能会添加的配置。 虽然你可以创建小到/29 的网关子网，但如果你有可用的地址空间，则建议你创建/27 或更大（/27、/26 等）的网关子网。 这将适合大多数配置。

以下 Resource Manager PowerShell 示例显示名为 GatewaySubnet 的网关子网。 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>区域冗余型网关 SKU

也可以在 Azure 可用性区域中部署 ExpressRoute 网关。 这在物理上和逻辑上将它们分成不同的可用区域，从而保护本地网络与 Azure 的连接免受区域级故障的影响。

![区域冗余型 ExpressRoute 网关](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

区域冗余型网关使用 ExpressRoute 网关的特定新网关 SKU。

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

新的网关 SKU 还支持其他部署选项，以最好地满足你的需求。 使用新网关 SKU 创建虚拟网络网关时，还可以选择在特定区域中部署网关。 这称为区域网关。 部署区域网关时，网关的所有实例都部署在同一可用性区域中。

## <a name="fastpath"></a>FastPath

ExpressRoute 虚拟网络网关旨在交换网络路由和路由网络流量。 FastPath 旨在提高本地网络与虚拟网络之间的数据路径性能。 启用后，FastPath 会将网络流量直接发送到虚拟网络中的虚拟机，绕过网关。

有关 FastPath 的详细信息，包括限制和要求，请参阅[关于 FastPath](about-fastpath.md)。

## <a name="resources"></a>REST API 和 PowerShell cmdlet
有关将 REST API 和 PowerShell cmdlet 用于虚拟网络网关配置的其他技术资源和特定语法要求，请参阅以下页面：

| **经典** | **资源管理器** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>后续步骤

有关可用连接配置的详细信息，请参阅[ExpressRoute 概述](expressroute-introduction.md)。

有关创建 ExpressRoute 网关的详细信息，请参阅为[ExpressRoute 创建虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。

有关配置区域冗余网关的详细信息，请参阅[创建区域冗余虚拟网络网关](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)。

有关 FastPath 的详细信息，请参阅[关于 FastPath](about-fastpath.md)。