---
title: 方案： Vnet 的自定义隔离
titleSuffix: Azure Virtual WAN
description: 路由方案-阻止所选 Vnet 相互联系
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4443c92fad2510b6bc4bc1214840aca5553556a5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553455"
---
# <a name="scenario-custom-isolation-for-vnets"></a>方案： Vnet 的自定义隔离

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在 Vnet 的自定义隔离方案中，目标是防止特定 Vnet 集能够访问其他特定的 Vnet 集。 但是，Vnet 需要访问所有分支 (VPN/ER/用户 VPN) 。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-design"></a><a name="design"></a>方案设计

为了确定需要多少个路由表，你可以构建一个连接矩阵。 对于这种情况，它将如下所示，其中每个单元表示源 (行) 是否可以与目标 (列) ：

| From | 到:| *Blue Vnet* | *Red Vnet* | *分支*|
|---|---|---|---|---|
| **Blue Vnet** |   &#8594;|      X        |               |       X      |
| **Red Vnet**  |   &#8594;|              |       X       |       X      |
| **分支**   |   &#8594;|     X        |       X       |       X      |

上表中的每个单元都说明了虚拟 WAN 连接 (流的 "From" 端、表中的行标题) 了解流的 "To" 端 (目标前缀，表) 中的列标题用于特定的流量流。

不同的行模式数将是我们在此方案中将需要的路由表的数量。 在这种情况下，我们将为虚拟网络调用**RT_BLUE**和**RT_RED** ，并为分支**默认**提供三个路由路由表。 请记住，分支始终必须与默认路由表相关联。

分支需要了解来自红和蓝 Vnet 的前缀，因此所有 Vnet 都需要传播到默认 (另外**RT_BLUE**或**RT_RED**) 。 Blue 和 Red Vnet 将需要了解分支前缀，因此分支也将传播到**RT_BLUE**和**RT_RED**的路由表。 因此，这是最终设计：

* 蓝色虚拟网络：
  * 关联的路由表： **RT_BLUE**
  * 传播到路由表： **RT_BLUE**和**默认值**
* 红色虚拟网络：
  * 关联的路由表： **RT_RED**
  * 传播到路由表： **RT_RED**和**默认值**
* 转
  * 关联的路由表：**默认值**
  * 传播到路由表： **RT_BLUE**、 **RT_RED**和**默认值**

> [!NOTE]
> 由于所有分支都需要与默认路由表相关联，并且要传播到同一组路由表，因此所有分支都将具有相同的连接配置文件。 换句话说，Vnet 的红色/蓝色概念不能应用于分支。

> [!NOTE]
> 如果虚拟 WAN 部署在多个区域，则需要在每个中心创建**RT_BLUE**和**RT_RED**路由表，并且每个 VNet 连接的路由都需要使用传播标签传播到每个虚拟中心的路由表。

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="architecture"></a>方案工作流

在**图 1**中，有蓝色和红色 VNet 连接。

* 蓝色连接的 Vnet 可以相互联系，并 (VPN/ER/P2S) 连接的所有分支。
* 红色 Vnet 可以相互联系，并 (VPN/ER/P2S) 连接的所有分支。

设置路由时，请考虑以下步骤。

1. 在 Azure 门户中创建两个自定义路由表， **RT_BLUE**和**RT_RED**。
2. 对于路由表**RT_BLUE**，为以下设置：
   * **关联**：选择所有蓝色 vnet。
   * **传播**：对于 "分支"，请选择 "分支" 选项，表示 "分支 (VPN/ER/P2S) 连接会将路由传播到此路由表。
3. 对 (VPN/ER/P2S) 的红色 Vnet 和分支**RT_RED**路由表重复相同的步骤。

这将导致路由配置更改，如下图所示

**图 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="图1":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
