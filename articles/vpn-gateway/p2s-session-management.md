---
title: Azure VPN 网关：点到站点 VPN 会话管理
description: 本文可帮助你查看点到站点 VPN 会话并断开其连接。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274227"
---
# <a name="point-to-site-vpn-session-management"></a>点到站点 VPN 会话管理

Azure 虚拟网络网关提供一种简单的方法来查看和断开当前的点到站点 VPN 会话的连接。 本文可帮助你查看和断开当前会话的连接。

>[!NOTE]
>会话状态每5分钟更新一次。 它不会立即更新。
>

## <a name="portal"></a>门户

查看并断开门户中的会话：

1. 导航到 VPN 网关。
1. 在 " **监视** " 部分下，选择 " **点到站点会话**"。

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="门户示例&quot;:::
1. 可以查看 windowpane> 中的所有当前会话。
1. 对于要断开连接的会话，请选择 **&quot;...&quot;** ，然后选择 " **断开连接**"。

## <a name="powershell"></a>PowerShell

使用 PowerShell 查看并断开会话：

1. 运行以下 PowerShell 命令以列出活动会话：

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. 复制要断开连接的会话的 **VpnConnectionId** 。

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="门户示例&quot;:::
1. 可以查看 windowpane> 中的所有当前会话。
1. 对于要断开连接的会话，请选择 **&quot;...&quot;** ，然后选择 ":::
1. 若要断开会话连接，请运行以下命令：

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>后续步骤

有关点到站点连接的详细信息，请参阅 [关于点到站点 VPN](point-to-site-about.md)。
