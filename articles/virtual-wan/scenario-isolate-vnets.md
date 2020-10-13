---
title: 方案：隔离 VNet
titleSuffix: Azure Virtual WAN
description: 用于路由的方案 - 隔离 VNet
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f725932b30fad062123d6c752f2d563b84f98b2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267629"
---
# <a name="scenario-isolating-vnets"></a>方案：隔离 VNet

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此方案中，目标是要防止 VNet 之间相互通信。 这称为隔离 VNet。 有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

在此方案中，某个特定 VNet 内的工作负载保持被隔离状态，无法与其他 VNet 通信。 但是，这些 VNet 需要与所有分支（VPN、ER 和用户 VPN）通信。 为了确定将会需要多少个路由表，可以构建一个连接矩阵。 对于此方案，它将如下表所示，其中每个单元格表示源（行）是否可以与目标（列）通信：

| 源 |   目标 |  VNet | *分支* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;|           |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中每个单元格都描述了虚拟 WAN 连接（流的“源”端，行标题）是否为特定流量流获取目标前缀（流的“目标”端，斜体的列标题），其中“X”表示由虚拟 WAN 提供连接。

此连接矩阵提供了两种不同的行模式，它们会转换为两个路由表。 虚拟 WAN 已经有一个 Default 路由表，所以我们需要有另一个路由表。 在此示例中，我们将该路由表命名为 RT_VNET。

VNet 将会与此 RT_VNET 路由表关联。 由于它们需要连接到分支，因此分支需要传播到 RT_VNET（否则 VNet 无法获取分支前缀）。 由于分支始终关联到 Default 路由表，因此 VNet 需要传播到 Default 路由表。 这样，最终设计如下：

* 虚拟网络：
  * 关联的路由表：RT_VNET
  * 传播到路由表：**Default**
* 分支：
  * 关联的路由表：**Default**
  * 传播到路由表：RT_VNET 和 Default 

请注意，由于只有分支传播到 RT_VNET 路由表，因此只有这些才是 VNet 将要获取的前缀，而其他 VNet 的分支则不是。

有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流

若要配置此方案，请考虑以下步骤：

1. 在每个中心创建自定义路由表。 在此示例中，路由表为 RT_VNet。 若要创建路由表，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。 若要详细了解路由表，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
2. 创建 RT_VNet 路由表时，请配置以下设置：

   * **Association**：选择要隔离的 VNet。
   * 传播：为分支选择选项，意味着分支 (VPN/ER/P2S) 连接会将路由传播到此路由表。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="隔离的 VNet":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
