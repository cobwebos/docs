---
title: 方案：用于虚拟 WAN 的 Azure 防火墙自定义路由
titleSuffix: Azure Virtual WAN
description: 路由方案 - 在 VNet 之间直接路由流量，但对 VNet 到 Internet/分支和分支到 VNet 的流量流使用 Azure 防火墙
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 301bc64bee291fa25506e7f435e923be7e244cd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267510"
---
# <a name="scenario-azure-firewall---custom"></a>方案：Azure 防火墙 - 自定义

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此方案中，目标是在 VNet 之间直接路由流量，但对 VNet 到 Internet/分支和分支到 VNet 的流量流使用 Azure 防火墙。

## <a name="design"></a><a name="design"></a>设计

为了确定需要多少路由表，可以构建一个连接矩阵，其中每个单元格都表示源（行）是否可以与目标（列）通信。 此方案中的连接矩阵很普通，但需要与其他方案保持一致，仍然有待我们进行研究。

**连接矩阵**

| 源           | 到:      | *VNet*      | *分支*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **VNet**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **分支**   |   &#8594;|    AzFW      |       X       |       X      |

在上一个表中，“X”表示两个连接之间的直接连接，流量不穿过虚拟 WAN 中的 Azure 防火墙，而“AzFW”表示该流将通过 Azure 防火墙。 由于矩阵中有两个不同的连接模式，我们将需要两个路由表，这两个表将配置如下：

* 虚拟网络：
  * 关联的路由表：**RT_VNet**
  * 传播到路由表：**RT_VNet**
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：**默认**

> [!NOTE]
> 你可以在每个区域中使用单个安全虚拟中心创建一个单独的虚拟 WAN 实例，然后可以通过站点到站点 VPN 将每个虚拟 WAN 互相连接。

有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流

在此方案中，你希望通过 Azure 防火墙为 VNet 到 Internet、VNet 到分支或分支到 VNet 的流量路由流量，但想要直接路由 VNet 到 VNet 的流量。 如果使用了 Azure 防火墙管理器，则路由设置会自动填充到默认路由表中。 专用流量适用于 VNet 和分支，Internet 流量适用于 0.0.0.0/0。

VPN、ExpressRoute 和用户 VPN 连接统称为“分支”，并与同一（默认）路由表关联。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。 若要配置此方案，请考虑以下步骤：

1. 创建自定义路由表 **RT_VNet**。
1. 创建用于激活 VNet 到 Internet 和 VNet 到分支的路由：0.0.0.0/0 且下一跃点指向 Azure 防火墙。 在“传播”部分中，确保选择了 VNet，这将确保创建更具体的路由，从而允许 VNet 到 VNet 的直接流量流。

   * 在“关联”中：选择 VNet，这表示 VNet 将根据此路由表的路由到达目标。
   * 在“传播”中：选择 VNet，这表示 VNet 传播到此路由表；换而言之，更具体的路由将传播到此路由表，从而确保 VNet 到 VNet 之间的直接流量流。

1. 将 VNet 的聚合静态路由添加到默认路由表中，以通过 Azure 防火墙激活分支到 VNet 流。

   * 请记住，分支是关联的并传播到默认路由表。
   * 分支不会传播到 RT_VNet 路由表。 这样可通过 Azure 防火墙确保 VNet 到分支的流量流。

这将导致路由配置更改，如图 1 所示。

**图 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="图 1":::

> [!NOTE]
> 虚拟 WAN 中心和连接的虚拟网络应当位于同一 Azure 区域中。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
* 若要详细了解如何配置虚拟中心路由，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。
