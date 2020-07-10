---
title: 方案：通过 NVA 路由流量
titleSuffix: Azure Virtual WAN
description: 通过 NVA 路由流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142968"
---
# <a name="scenario-route-traffic-through-an-nva"></a>方案：通过 NVA 路由流量

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此 NVA 方案中，目标是将流量路由到分支到 VNet 的 NVA (网络虚拟) 设备，并将 VNet 路由到分支。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

> [!NOTE]
> 某些路由功能可能仍在推出。如果你所在的区域中尚未发生此推出，请使用以下文章中的步骤：
>* [Azure 门户文章](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 文章](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>方案体系结构

**图 1**提供了两个中心：**集线器 1**和**中心 2**。

* **集线器 1**和**中心 2**直接连接到 NVA Vnet **VNET 2**和**vnet 4**。

* **Vnet 5**和**Vnet 6**与**vnet 2**对等互连。

* **Vnet 7**和**Vnet 8**与**vnet 4**对等互连。

* **Vnet 5、6、7、8**是间接轮辐，不直接连接到虚拟中心。

**图1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="图1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要通过 NVA 设置路由，请执行以下步骤：

1. 标识 NVA 辐射 VNet 连接。 在**图 1**中，它们是**vnet 2 连接 (Eastusconn) **和**VNET 4 连接 (weconn) **。

   确保已设置 Udr：
   * 从 VNET 5 和6到 VNET 2 NVA IP
   * 从 VNET 7 和8到 VNET 4 NVA IP 
   
   不需要将 VNET 5、6、7和8直接连接到虚拟中心。 确保 Vnet 5、6、7、8中的 Nsg 允许分支 (VPN/ER/P2S) 或 Vnet 连接到 theire 远程 Vnet 的流量。 例如，VNET 5、6必须确保 Nsg 允许连接到远程中心2的本地地址前缀和 Vnet 7、8的流量。 

2. 将 Vnet 2、5、6的聚合静态路由条目添加到中心1的默认路由表。 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="示例":::

3. 为 VNET 2 的虚拟网络连接中的 Vnet 5、6配置静态路由。 若要为虚拟网络连接设置路由配置，请参阅[虚拟中心路由](how-to-virtual-hub-routing.md#routing-configuration)。

4. 将 Vnet 4、7、8的聚合静态路由条目添加到中心1的默认路由表。

5. 对于中心2的默认路由表，重复步骤2、3和4。

这将导致路由配置更改，如下图所示

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="结果":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
