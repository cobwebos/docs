---
title: 虚拟 WAN：创建虚拟中心路由表到 NVA： Azure 门户
description: 使用门户将流量传输到网络虚拟设备的虚拟 WAN 虚拟中心路由表。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: c0681024b60827cf589906041c264d912ab209bb
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612354"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>为 Nva 创建虚拟 WAN 中心路由表： Azure 门户

本文介绍如何通过网络虚拟设备（NVA）将流量从连接到虚拟 WAN 集线器的分支（本地站点）转移到分支 Vnet。

![虚拟 WAN 示意图](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>开始之前

验证是否符合以下条件：

*  你拥有网络虚拟设备（NVA）。 网络虚拟设备是所选的第三方软件，通常是从虚拟网络中的 Azure Marketplace 进行预配的。

    * 必须将专用 IP 地址分配给 NVA 网络接口。

    * 未在虚拟中心部署 NVA。 它必须部署在单独的 VNet 中。

    *  NVA VNet 可能有一个或多个虚拟网络连接到它。 在本文中，我们将 NVA VNet 称为 "间接辐射 VNet"。 可使用 VNet 对等互连将这些 Vnet 连接到 NVA VNet。 Vnet 1、Vnet 2 和 NVA Vnet 之间的上图中的黑色箭头描述了 Vnet 对等互连链接。
*  您已经创建了2个 Vnet。 它们将用作辐射 Vnet。

    * 对于本练习，VNet 辐射地址空间为： VNet1： 10.0.2.0/24 和 VNet2： 10.0.3.0/24。 如果需要有关如何创建 VNet 的信息，请参阅[创建虚拟网络](../virtual-network/quick-create-portal.md)。

    * 请确保任何 Vnet 中没有虚拟网络网关。
    * 对于此配置，这些 Vnet 不需要网关子网。

## <a name="signin"></a>1. 登录

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

## <a name="vwan"></a>2. 创建虚拟 WAN

创建虚拟 WAN。 对于本练习，可以使用以下值：

* **虚拟 WAN 名称：** myVirtualWAN
* **资源组：** testRG
* **位置：** 美国西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. 创建集线器

创建中心。 对于本练习，可以使用以下值：

* **位置：** 美国西部
* **名称：** westushub
* **中心专用地址空间：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. 创建和应用中心路由表

使用集线器路由表更新中心。 对于本练习，可以使用以下值：

* **分支 VNet 地址空间：** （VNet1 和 VNet2） 10.0.2.0/24 和 10.0.3.0/24
* **DMZ NVA 网络接口专用 IP 地址：** 10.0.4。5

1. 导航到虚拟 WAN。
2. 单击要为其创建路由表的中心。
3. 单击 " **...** "，然后单击 "**编辑虚拟中心**"。
4. 在 "**编辑虚拟中心**" 页上，向下滚动并选中 "**使用表进行路由**" 复选框。
5. 在 "**如果目标前缀为**" 列中，添加地址空间。 在 "**发送到下一跃点**" 列中，添加 DMZ NVA 网络接口专用 IP 地址。
6. 单击 "**确认**" 以更新包含路由表设置的集线器资源。

## <a name="connections"></a>5. 创建 VNet 连接

创建从每个间接辐射 VNet （VNet1 和 VNet2）到中心的 Vnet 连接。 这些 Vnet 连接由上图中的蓝色箭头描述。 然后，创建从 NVA VNet 到集线器的 Vnet 连接（图中的黑色箭头）。 

 对于此步骤，可以使用以下值：

| VNet 名称| 连接名称|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

为要连接的每个 VNet 重复以下过程。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。
2. 在虚拟网络连接页上，单击“+添加连接”。
3. 在“添加连接”页上填写以下字段：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”以创建连接。

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
