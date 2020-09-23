---
title: 查看虚拟中心的有效路由： Azure 虚拟 WAN |Microsoft Docs
description: 如何在 Azure 虚拟 WAN 中查看虚拟中心的有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983657"
---
# <a name="view-virtual-hub-effective-routes"></a>查看虚拟中心有效路由

可以在 Azure 门户中查看虚拟 WAN 集线器的所有路由。 本文将指导你完成查看有效路由的步骤。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>选择连接或路由表

1. 导航到虚拟中心，然后选择 " **路由**"。 在 "路由" 页上，选择 " **有效路由**"。
1. 您可以从下拉列表中选择 " **路由表**"。 如果看不到路由表选项，这意味着在此虚拟中心中没有设置自定义路由表或默认路由表。

## <a name="view-output"></a><a name="output"></a>查看输出

页面输出显示以下字段：

* **前缀**：从虚拟中心路由器 (了解到的当前实体已知的地址前缀) 
* **下一跃点类型**：可以是虚拟网络连接、VPN_S2S_Gateway、ExpressRouteGateway、远程集线器或 Azure 防火墙。
* **下一跃点**：这是指向下一跃点的资源 ID 的链接，或者只是显示链接以表示当前中心。
* **源**：路由源的资源 ID。
* **As Path**： BGP Attribute as (自治 system) Path 列出了需要遍历以到达路径附加到的前缀的位置的所有 AS 编号，从该位置播发。

### <a name="example"></a><a name="example"></a>示例

以下示例表中的值表示虚拟中心连接或路由表已了解 10.2.0.0/24 (分支前缀) 的路由。 由于 **Vpn 下一跃点类型** 与 **下一个跃点** VPN 网关资源 ID VPN_S2S_Gateway，它已获知路由。 **路由源** 指向起始 VPN 网关/路由表/连接的资源 ID。 **As path** 指示分支的 as 路径。

使用表底部的滚动条查看 "AS Path"。

| **前缀** |  **下一跃点类型** | **下一跃点** |  **路由源** |**作为路径** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**注意事项：**

* 如果在 **获取有效路由** 输出中看到 0.0.0.0/0，则表示该路由存在于某个路由表中。 但是，如果此路由是为 internet 设置的，则连接上需要一个附加标志 **"enableInternetSecurity"： true** 。 如果连接上的 "enableInternetSecurity" 标志为 "false"，则 VM NIC 上的有效路由不会显示路由。

* 编辑虚拟网络连接、VPN 连接或 ExpressRoute 连接时，会在 Azure 虚拟 WAN 门户中查看 " **传播默认路由** " 字段。 此字段指示 **enableInternetSecurity** 标志，该标志对于 EXPRESSROUTE 和 VPN 连接始终默认为 "false"，对于虚拟网络连接始终为 "true"。

* 查看 VM NIC 上的有效路由时，如果将下一跃点视为 "虚拟网络网关"，则在 VM 位于连接到虚拟 WAN 集线器的分支中时，会显示虚拟中心路由器。

* 仅当虚拟中心至少具有一种连接类型（ (VPN/ER/VNET) 连接到它）时，才会填充虚拟中心路由表的有效路由。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
