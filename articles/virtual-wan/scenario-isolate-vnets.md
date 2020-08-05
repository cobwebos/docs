---
title: 方案：隔离 Vnet
titleSuffix: Azure Virtual WAN
description: 路由隔离 Vnet 的方案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 763a13cf2ecbe845619101bc9e325cc51564260a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553387"
---
# <a name="scenario-isolating-vnets"></a>方案：隔离 Vnet

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在这种情况下，目标是防止 Vnet 到达其他。 这称为隔离 Vnet。 有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

在此方案中，某个 VNet 内的工作负荷保持隔离，并且无法与其他 Vnet 通信。 但是，Vnet 需要访问所有分支 (VPN、ER 和用户 VPN) 。 为了确定需要多少个路由表，你可以构建一个连接矩阵。 对于这种情况，它将如下表所示，其中每个单元表示 (行) 的源是否可以与目标 (列) ：

| From |   功能 |  *Vnet* | *分支* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;|           |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中的每个单元都说明了虚拟 WAN 连接 (流的 "From" 端、行标题) 了解流的 "To" 端 (目标前缀、特定流量流的斜体) 中的列标题。

此连接矩阵提供两个不同的行模式，它们转换为两个路由表。 虚拟 WAN 已经有一个默认路由表，因此我们将需要另一个路由表。 对于此示例，我们将**RT_VNET**的路由表命名为。

Vnet 将与此**RT_VNET**路由表相关联。 由于需要连接到分支，因此分支需要传播到**RT_VNET** (否则 vnet 将无法了解分支前缀) 。 由于分支始终关联到默认的路由表，因此 Vnet 需要传播到默认路由表。 因此，这是最终设计：

* 虚拟网络：
  * 关联的路由表： **RT_VNET**
  * 传播到路由表：**默认值**
* 转
  * 关联的路由表：**默认值**
  * 传播到路由表： **RT_VNET**和**默认值**

请注意，由于只有分支传播到**RT_VNET**的路由表，因此它们将是 vnet 将学习的唯一前缀，而不是其他 vnet 的前缀。

有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流

若要配置此方案，请考虑以下步骤：

1. 在每个中心创建自定义路由表。 在此示例中，路由表是**RT_VNet**的。 若要创建路由表，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。 有关路由表的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
2. 创建**RT_VNet**路由表时，请配置以下设置：

   * **关联**：选择要隔离的 vnet。
   * **传播**：选择分支的选项，表示分支 (VPN/ER/P2S) 连接会将路由传播到此路由表。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="独立 Vnet":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
