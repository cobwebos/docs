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
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267510"
---
# <a name="scenario-azure-firewall---custom"></a>方案：Azure 防火墙 - 自定义

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在这种情况下，目标是直接在 Vnet 之间路由流量，但将 Azure 防火墙用于 VNet 到 Internet/分支以及分支到 VNet 的流量流。

## <a name="design"></a><a name="design"></a>设计

为了确定需要多少个路由表，你可以构建一个连接矩阵，其中每个单元表示源 (行) 是否可以与) 的目标 (列通信。 此方案中的连接矩阵非常简单，但与其他方案一致，我们仍然可以查看它。

**连接矩阵**

| 源           | 到:      | *Vnet*      | *分支*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **Vnet**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **分支**   |   &#8594;|    AzFW      |       X       |       X      |

在上表中，"X" 表示两个连接之间的直接连接，而不会遍历虚拟 WAN 中的 Azure 防火墙，而 "AzFW" 指示流将经历 Azure 防火墙。 由于矩阵中有两个不同的连接模式，我们将需要两个路由表，这些表将配置如下：

* 虚拟网络：
  * 关联的路由表： **RT_VNet**
  * 传播到路由表： **RT_VNet**
* 转
  * 关联的路由表： **默认值**
  * 传播到路由表： **默认值**

> [!NOTE]
> 你可以使用每个区域中的单个安全虚拟中心创建一个单独的虚拟 WAN 实例，然后可以通过站点到站点 VPN 将每个虚拟 WAN 连接到对方。

有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流

在此方案中，你希望通过 Azure 防火墙为 VNet 到 Internet、VNet 到分支或分支到 VNet 的流量路由流量，但想要直接路由 VNet 到 VNet 的流量。 如果使用了 Azure 防火墙管理器，则路由设置会自动填充到默认路由表中。 专用流量适用于 VNet 和分支，Internet 流量适用于 0.0.0.0/0。

VPN、ExpressRoute 和用户 VPN 连接统称为“分支”，并与同一（默认）路由表关联。 所有 VPN、ExpressRoute 和用户 VPN 连接将路由传播到同一组路由表。 若要配置此方案，请考虑以下步骤：

1. **RT_VNet**创建自定义路由表。
1. 创建用于激活 VNet 到 Internet 和 VNet 到分支的路由：0.0.0.0/0 且下一跃点指向 Azure 防火墙。 在“传播”部分中，确保选择了 VNet，这将确保创建更具体的路由，从而允许 VNet 到 VNet 的直接流量流。

   * **关联：** 选择 vnet，这意味着 vnet 将根据此路由表的路由到达目标。
   * 在 "传播" 中 **：** 选择 vnet，这将意味着 vnet 传播到此路由表;换句话说，更具体的路由将传播到此路由表，从而确保 VNet 到 VNet 之间的直接流量流动。

1. 将 VNet 的聚合静态路由添加到默认路由表中，以通过 Azure 防火墙激活分支到 VNet 流。

   * 请记住，分支是关联的并传播到默认路由表。
   * 分支不会传播到 RT_VNet 路由表。 这样可通过 Azure 防火墙确保 VNet 到分支的流量流。

这将导致路由配置更改，如图 1 所示。

**图 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="图 1":::

> [!NOTE]
> 虚拟 WAN 中心和连接的虚拟网络应位于同一 Azure 区域。

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
* 有关如何配置虚拟中心路由的详细信息，请参阅 [如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。
