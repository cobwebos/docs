---
title: 方案：隔离 Vnet
titleSuffix: Azure Virtual WAN
description: 路由隔离 Vnet 的方案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567123"
---
# <a name="scenario-isolating-vnets"></a>方案：隔离 Vnet

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在这种情况下，目标是防止 Vnet 到达其他。 这称为隔离 Vnet。 VNet 中的工作负荷保持隔离，并且不能与其他 Vnet 通信，就像在任何情况下一样。 但是，需要 Vnet 来访问所有分支（VPN、ER 和用户 VPN）。 在这种情况下，所有 VPN、ExpressRoute 和用户 VPN 连接都关联到同一个路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要配置此方案，请考虑以下步骤：

1. 创建自定义路由表。 在此示例中，路由表是**RT_VNet**的。 若要创建路由表，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。 有关路由表的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
2. 创建**RT_VNet**路由表时，请配置以下设置：

   * **关联**：选择要隔离的 vnet。
   * **传播**：选择分支选项，表示分支（VPN/ER/P2S）连接会将路由传播到此路由表。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="独立 Vnet":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
