---
title: 创建 Azure 虚拟 WAN 虚拟中心路由表-Azure 门户 |Microsoft Docs
description: 虚拟 WAN 虚拟中心路由表，引导到网络虚拟设备使用门户的流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461648"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>创建 Nva 虚拟 WAN 的中心路由表：Azure 门户

本文介绍如何控制流量从一个中心到网络虚拟设备 (NVA)。

![虚拟 WAN 示意图](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>开始之前

验证是否符合以下条件：

*  您有网络虚拟设备 (NVA)。 网络虚拟设备是从 Azure Marketplace 通常虚拟网络中设置所选的第三方软件。

    * 必须给 NVA 网络接口分配专用 IP 地址。

    * 未将 NVA 部署在虚拟中心。 它必须部署在单独的 VNet 中。

    *  NVA VNet 只能有一个或多个虚拟网络连接到它。 在本文中，我们引用 NVA VNet 作为间接辐射 VNet。 这些 Vnet 可以使用 VNet 对等互连连接到 NVA VNet。
*  您已创建 2 个 Vnet。 它们将作为辐射 Vnet 使用。

    * 对于此练习中，VNet 辐射地址空间为：VNet1：10.0.2.0/24 和 VNet2:10.0.3.0/24. 如果需要有关如何创建 VNet 的信息，请参阅[创建虚拟网络](../virtual-network/quick-create-portal.md)。

    * 请确保有的任何 Vnet 中没有虚拟网络网关。
    * 对于此配置，这些 Vnet 不需要网关子网。

## <a name="signin"></a>1.登录

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

## <a name="vwan"></a>2.创建虚拟 WAN

创建虚拟 WAN。 对于此练习的目的，可以使用以下值：

* **虚拟 WAN 名称：** myVirtualWAN
* **资源组：** testRG
* **位置：** 美国西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3.创建中心

创建中心。 对于此练习的目的，可以使用以下值：

* **位置：** 美国西部
* **名称：** westushub
* **中心专用地址空间：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4.创建并应用中心路由表

使用中心路由表更新中心。 对于此练习的目的，可以使用以下值：

* **间接辐射 VNet 的地址空间：** （VNet1 和 VNet2） 10.0.2.0/24 和 10.0.3.0/24
* **外围网络 NVA 网络接口专用 IP 地址：** 10.0.4.5

1. 导航到你的虚拟 WAN。
2. 单击你想要创建路由表的中心。
3. 单击 **...** ，然后单击**编辑虚拟中心**。
4. 上**编辑虚拟中心**页上，向下滚动并选中该复选框**使用的路由表**。
5. 在中**如果目标前缀**列中，添加地址空间。 在中**将发送到下一跃点**列中，添加外围网络 NVA 网络接口专用 IP 地址。
6. 单击**确认**使用路由表设置更新中心资源。

## <a name="connections"></a>5.创建 VNet 连接

创建从每个间接辐射 VNet （VNet1 和 VNet2） 到中心的连接。 然后，创建从 NVA VNet 连接到中心。

 对于此步骤中，可以使用以下值：

| VNet 名称| 连接名称|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

重复以下过程为每个你想要连接的 VNet。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。 
2. 在虚拟网络连接页上，单击“+添加连接”。 
3. 在“添加连接”页上填写以下字段  ：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”以创建连接。 

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。