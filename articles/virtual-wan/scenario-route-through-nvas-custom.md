---
title: 方案：使用自定义设置通过 Nva 路由流量
titleSuffix: Azure Virtual WAN
description: 此方案有助于通过 Nva 使用不同的 NVA 路由流量来路由流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567425"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>方案：通过 Nva （预览版）路由流量

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此 NVA （网络虚拟设备）方案中，目标是通过 NVA for VNet < > 分支来路由流量，并使用不同的 NVA 来实现 Internet 绑定的流量。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>方案体系结构

**图 1**中有一台集线器**1**。

* **集线器 1**直接连接到 NVA vnet **Vnet 4**和**vnet 5**。

* Vnet 1、2和3之间的流量以及分支（VPN/ER/P2S）应通过**VNET 4 NVA** 10.4.0.5。

* 来自 Vnet 1、2和3的所有 internet 绑定流量应通过**VNET 5 NVA** 10.5.0.5。

**图1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="图1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要通过 NVA 设置路由，请执行以下步骤：

1. 为了使 Internet 绑定的流量通过 VNET5，需要 Vnet 1、2和3才能直接通过 VNet 对等互连连接到 VNET 5。 还需要在 Vnet 中设置为 0.0.0.0/0 和下一跃点10.5.0.5 的 UDR。 目前，虚拟 WAN 不允许虚拟中心的下一跃点 NVA 0.0.0.0/0。

1. 在 Azure 门户中，导航到你的虚拟中心，并创建自定义路由表**RT_Shared** ，通过从所有 Vnet 和分支连接传播来了解路由。 在**图 2**中，这是一个空的自定义路由表**RT_Shared**。

   * **路由：** 无需添加任何静态路由。

   * **关联：** 选择 Vnet 4 和5，这意味着 Vnet 4 和5连接关联到**RT_Shared**的路由表。

   * **传播：** 由于你希望所有分支和 VNet 连接都动态地将其路由传播到此路由表，因此请选择 "分支" 和 "所有 Vnet"。

1. 创建自定义路由表**RT_V2B**用于将流量从 vnet 1、2和3定向到分支。

   * **路由：** 使用下一跃点作为 VNET 4 连接，为分支（VPN/ER/P2S）（**图 2**中的 10.2.0.0/16）添加聚合静态路由条目。 还需要为分支前缀在 VNET 4 的连接中配置静态路由，并指示下一跃点是 VNET 4 中 NVA 的特定 IP。

   * **关联：** 选择所有 Vnet 1、2和3。 这意味着 VNet 连接1、2和3将关联到此路由表，并能够在此路由表中了解路由（静态和动态 via 传播）。

   * **传播：** 连接将路由传播到路由表。 选择 Vnet 1、2和3会启用将路由从 Vnet 1、2和3传播到此路由表。 由于分支 VNet 流量通过 VNET4 中的 NVA，因此无需将路由从分支连接传播到 RT_V2B。
  
1. 编辑默认路由表**DefaultRouteTable**。

   所有 VPN、ExpressRoute 和用户 VPN 连接都与默认路由表相关联。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。

   * **路由：** 为 Vnet 1、2和3（**图 2**中的 10.1.0.0/16）添加聚合静态路由条目，下一跃点为 VNET 4 连接。 还需要为 VNET 1、2和3聚合前缀在 VNET 4 的连接中配置静态路由，并指示下一跃点是 VNET 4 中 NVA 的特定 IP。

   * **关联：** 请确保选择了 "分支（VPN/ER/P2S）" 选项，确保本地分支连接与*defaultroutetable*相关联。

   * **传播自：** 请确保选择了 "分支（VPN/ER/P2S）" 选项，确保本地连接将路由传播到*defaultroutetable*。

**图2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="图2":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
