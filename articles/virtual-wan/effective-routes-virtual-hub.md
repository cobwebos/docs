---
title: 查看虚拟中心的有效路由： Azure 虚拟 WAN |Microsoft Docs
description: 如何查看 Azure 虚拟 WAN 中的虚拟中心的有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983657"
---
# <a name="view-virtual-hub-effective-routes"></a>查看虚拟中心有效路由

可以在 Azure 门户中查看虚拟 WAN 中心的所有路由。 本文将指导你完成查看有效路由的步骤。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>选择连接或路由表

1. 导航到虚拟中心，然后选择“路由”。 在“路由”页上，选择“有效路由”。
1. 可以从下拉列表中选择“路由表”。 如果看不到“路由表”选项，这意味着你在此虚拟中心中未设置自定义或默认路由表。

## <a name="view-output"></a><a name="output"></a>查看输出

页面输出显示以下字段：

* 前缀：当前实体已知的地址前缀（从虚拟中心路由器了解）
* 下一跃点类型：可以是虚拟网络连接、VPN_S2S_Gateway、ExpressRouteGateway、远程中心或 Azure 防火墙。
* **下一跃点**：这是指向下一跃点的资源 ID 的链接，或只是显示“在链路上”以表示当前中心。
* **源**：路由源的资源 ID。
* AS 路径：BGP 属性 AS（自治系统）路径会列出需要遍历的所有 AS 编号，以访问播发路径所附加到的前缀的位置。

### <a name="example"></a><a name="example"></a>示例

以下示例表中的值表示虚拟中心连接或路由表已了解 10.2.0.0/24（分支前缀）的路由。 由于“VPN 下一跃点类型”为 VPN_S2S_Gateway 并且“下一跃点”为 VPN 网关资源 ID，因此它已了解路由 。 “路由源”指向发起 VPN 网关/路由表/连接的资源 ID。 “AS 路径”表示分支的 AS 路径。

可以使用表底部的滚动条查看“AS 路径”。

| **Prefix** |  **下一跃点类型** | **下一跃点** |  **路由原点** |**AS 路径** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/`<sub id>`/resourceGroups/`<resource group name>`/providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/`<sub id>`/resourceGroups/`<resource group name>`/providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**注意事项：**

* 如果在“获取有效路由”输出中看到 0.0.0.0/0，则表示路由存在于一个路由表中。 但是，如果此路由是为 Internet 设置的，则连接中需要附加标志“enableInternetSecurity”：true。 如果连接中的“enableInternetSecurity”标志为“false”，则 VM NIC 上的有效路由不会显示路由。

* 当你编辑虚拟网络连接、VPN 连接或 ExpressRoute 连接时，Azure 虚拟 WAN 门户中会显示“传播默认路由”字段。 此字段指示 enableInternetSecurity 标志，该标志在默认情况下对于 ExpressRoute 和 VPN 连接始终为“false”，对于虚拟网络连接为“true”。

* 查看 VM NIC 上的有效路由时，如果将下一跃点视为“虚拟网络网关”，则在 VM 处于连接到虚拟 WAN 中心的分支中时，这会表示虚拟中心路由器。

* 仅当虚拟中心至少具有一种连接类型（ (VPN/ER/VNET) 连接到它）时，才会填充虚拟中心路由表的有效路由。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
