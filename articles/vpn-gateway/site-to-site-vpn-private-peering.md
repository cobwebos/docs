---
title: 通过 ExpressRoute 专用对等互连建立的站点到站点 VPN 连接
description: 本文可帮助你通过 ExpressRoute 专用对等互连启用站点到站点 VPN，以便对流量进行加密。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/08/2020
ms.author: cherylmc
ms.openlocfilehash: effbe8e771922ea07ad908dd4871f8dcdb7c1d19
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934273"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering-preview"></a>通过 ExpressRoute 专用对等互连 (预览 "配置站点到站点 VPN 连接) 

可以使用 RFC 1918 IP 地址通过 ExpressRoute 专用对等互连，将站点到站点 VPN 配置为虚拟网络网关。 此配置具有以下优势：

* 通过专用对等互连的流量已加密。

* 连接到虚拟网络网关的点到站点用户可以通过站点到站点隧道) 使用 ExpressRoute (来访问本地资源。

>[!NOTE]
>此功能仅在区域冗余网关上受支持。 例如，VpnGw1AZ、VpnGw2AZ 等。
>

若要完成此配置，请验证是否满足以下先决条件：

* 你有一个正常运行的 ExpressRoute 线路，该线路链接到将在其中 (或将) 创建 VPN 网关的 VNet。

* 可以通过 ExpressRoute 线路在 VNet 中通过 RFC1918 (专用) IP 来访问资源。

## <a name="routing"></a><a name="routing"></a>路由

**图 1** 显示了通过 ExpressRoute 专用对等互连的 VPN 连接的示例。 在此示例中，你将看到本地网络中的网络通过 ExpressRoute 专用对等互连连接到 Azure 集线器 VPN 网关。 此配置的一个重要方面是在本地网络与 Azure 之间通过 ExpressRoute 和 VPN 路径进行路由。

图 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="图 1":::

建立连接非常简单：

1. 与 ExpressRoute 线路和专用对等互连建立 ExpressRoute 连接。

1. 使用本文中的步骤建立 VPN 连接。

### <a name="traffic-from-on-premises-networks-to-azure"></a>从本地网络发往 Azure 的流量

对于从本地网络到 Azure 的流量，Azure 前缀通过 ExpressRoute 专用对等 BGP 和 VPN BGP 播发。 结果是从本地网络向 Azure) 方向 (两个网络路由：

•一个跨 IPsec 保护路径的网络路由。

•无 IPsec 保护直接通过 ExpressRoute 的一个网络路由。

若要将加密应用于通信，必须确保对于 **图 1**中的 VPN 连接网络，通过直接 ExpressRoute 路径首选 Azure 路由通过本地 VPN 网关。

### <a name="traffic-from-azure-to-on-premises-networks"></a>从 Azure 发往本地网络的流量

相同的要求适用于从 Azure 发往本地网络的流量。 为了确保优先使用 IPsec 路径而不是直接 ExpressRoute 路径（不受 IPsec 保护），可以采用两种做法：

• **为 vpn 连接的网络在 VPN BGP 会话上播发更具体的前缀**。 可以通过 ExpressRoute 专用对等互连播发包含已连接 VPN 的网络的更大范围，然后在 VPN BGP 会话中播发更具体的范围。 例如，通过 ExpressRoute 播发 10.0.0.0/16，通过 VPN 播发 10.0.1.0/24。

• **为 VPN 和 ExpressRoute 播发无交集前缀**。 如果已连接 VPN 的网络范围与已连接 ExpressRoute 的其他网络不相交，则可以分别在 VPN 和 ExpressRoute BGP 会话中播发这些前缀。 例如，通过 ExpressRoute 播发 10.0.0.0/24，通过 VPN 播发 10.0.1.0/24。

在这两个示例中，Azure 将通过 VPN 连接将流量发送到 10.0.1.0/24，而不是直接通过不受 VPN 保护的 ExpressRoute 发送。

>[!Warning]
>如果在 ExpressRoute 和 VPN 连接上公布相同的前缀，>Azure 将直接使用 ExpressRoute 路径，而无需 VPN 保护。
>

## <a name="portal-steps"></a><a name="portal"></a>门户步骤

1. 配置站点到站点连接。 有关步骤，请参阅 [站点到站点配置](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 一文。 务必为网关选择区域冗余的网关 SKU。 区域冗余 Sku 在 SKU 末尾有 "AZ"。 例如，VpnGw1AZ。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="网关专用 Ip":::
1. 在网关上启用专用 Ip。 选择 " **配置**"，并将 " **网关专用 ip** " 设置为 " **启用**"。 选择“保存”以保存更改。
1. 在 " **概述** " 页上，选择 " **查看更多** " 来查看专用 IP 地址。 记下此信息，以便稍后在配置步骤中使用。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="概述页" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. 若要启用对连接 **使用 Azure 专用 IP 地址** ，请选择 "  **配置**"。 将 " **使用 Azure 专用 IP 地址** " 设置为 " **已启用**"，然后选择 " **保存**"。

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="网关专用 Ip-已启用":::
1. 在防火墙中，对你在步骤3中记下的专用 IP 进行 ping 操作。 专用 IP 应可通过 ExpressRoute 专用对等互连进行访问。
1. 使用此专用 IP 作为本地防火墙上的远程 IP，通过 ExpressRoute 专用对等互连建立站点到站点隧道。

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell 步骤

1. 配置站点到站点连接。 有关步骤，请参阅 [配置站点到站点 VPN](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 一文。 务必为网关选择区域冗余的网关 SKU。 区域冗余 Sku 在 SKU 末尾有 "AZ"。 例如，VpnGw1AZ。
1. 将标志设置为使用以下 PowerShell 命令在网关上使用专用 IP：

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   应该会看到一个公用和专用 IP 地址。 写入输出的 "TunnelIpAddresses" 部分下的 IP 地址。 稍后的步骤中将使用此信息。
1. 使用以下 PowerShell 命令将连接设置为使用专用 IP 地址：

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. 在防火墙中，对你在步骤2中记下的专用 IP 进行 ping 操作。 它应可通过 ExpressRoute 专用对等互连进行访问。
1. 使用此专用 IP 作为本地防火墙上的远程 IP，通过 ExpressRoute 专用对等互连建立站点到站点隧道。

## <a name="next-steps"></a>后续步骤

有关 VPN 网关的详细信息，请参阅 [什么是 Vpn 网关？](vpn-gateway-about-vpngateways.md)
