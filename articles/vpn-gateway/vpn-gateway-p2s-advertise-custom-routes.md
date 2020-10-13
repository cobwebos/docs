---
title: Azure VPN 网关：为 P2S VPN 客户端播发自定义路由
description: 向点到站点客户端播发自定义路由的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392385"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>为 P2S VPN 客户端播发自定义路由

你可能想要向所有点到站点 VPN 客户端播发自定义路由。 例如，当你在 VNet 中启用了存储终结点并希望远程用户能够通过 VPN 连接访问这些存储帐户时。 可以向所有远程用户播发存储终结点的 IP 地址，以使到存储帐户的流量通过 VPN 隧道，而不是公共 Internet。

![Azure VPN 网关多站点连接示例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>播发自定义路由

若要播发自定义路由，请使用 `Set-AzVirtualNetworkGateway cmdlet`。 以下示例演示如何播发 [Contoso 存储帐户表](https://contoso.table.core.windows.net)的 IP。

1. Ping *contoso.table.core.windows.net* 并记录 IP 地址。 例如：

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 运行以下 PowerShell 命令：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 若要添加多个自定义路由，请使用逗号和空格分隔地址。 例如：

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>查看自定义路由

使用以下示例查看自定义路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>删除自定义路由

使用以下示例删除自定义路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>后续步骤

有关其他 P2S 路由信息，请参阅[关于点到站点路由](vpn-gateway-about-point-to-site-routing.md)。
