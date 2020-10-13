---
title: 将 Azure 虚拟 WAN 从 Basic 升级到标准-Azure 门户 |Microsoft Docs
description: 你可以升级虚拟 WAN 类型以获得更多功能。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447354"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>将虚拟 WAN 从基本层级升级到标准层级

本文可帮助你将基本 WAN 升级到标准 WAN。 “基本”WAN 类型将它中的所有中心都创建为基本 SKU 中心。 在基本中心内，只能使用站点到站点 VPN 功能。 “标准”WAN 类型将它中的所有中心都创建为标准 SKU 中心。 使用标准中心时，可通过 Azure 中心启用 ExpressRoute、用户（点到站点）VPN、完整网格中心和 VNet 到 VNet 传输。

下表显示了每种 WAN 类型的可用配置：

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>更改虚拟 WAN 类型

1. 在虚拟 WAN 的页面上，选择“配置”  以打开“配置”页面。

   ![显示 "配置" 页面的屏幕截图，其中包含有关升级到底部突出显示的标准类型虚拟 WAN 的信息文本框。](./media/upgrade-virtual-wan/1.png)
2. 对于 "虚拟 WAN 类型"，从下拉菜单中选择 " **标准** "。

   ![显示 "虚拟 WAN 类型" 下拉菜单的屏幕截图。](./media/upgrade-virtual-wan/2.png)
3. 请注意，如果升级到标准虚拟 WAN，将无法还原到基本虚拟 WAN。 如果要升级，请选择“确认”  。

   ![显示 "升级确认" 对话框的屏幕截图。](./media/upgrade-virtual-wan/4.png)
4. 保存更改后，你的虚拟 WAN 页面将与此示例类似。

   ![虚拟 WAN 示意图](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。