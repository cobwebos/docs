---
title: 将 Azure 虚拟 WAN 从基本版级升级到标准 - Azure 门户 |微软文档
description: 您可以升级虚拟 WAN 类型以获得更大的功能。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515806"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>将虚拟广域网从基本网域升级到标准

本文可帮助您将基本 WAN 升级到标准 WAN。 "基本"WAN 类型将其中的所有集线器创建为基本 SKU 集线器。 在基本中心中，您只能使用站点到站点 VPN 功能。 "标准"WAN 类型将其中的所有集线器创建为标准 SKU 集线器。 使用标准集线器时，可以启用 ExpressRoute、用户（点对点）VPN、全网状集线器和 VNet 到 VNet 传输通过 Azure 集线器。

下表显示了每种 WAN 类型的可用配置：

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>更改虚拟 WAN 类型

1. 在虚拟 WAN 的页面上，选择 **"配置"** 以打开"配置"页。

   ![虚拟 WAN 示意图](./media/upgrade-virtual-wan/1.png)
2. 对于虚拟广域网类型，请从下拉列表中选择 **"标准**"。

   ![虚拟 WAN 示意图](./media/upgrade-virtual-wan/2.png)
3. 请注意，如果升级到标准虚拟 WAN，则无法恢复为基本虚拟 WAN。 选择 **"确认**是否要升级"。

   ![虚拟 WAN 示意图](./media/upgrade-virtual-wan/4.png)
4. 保存更改后，虚拟 WAN 页面看起来与本示例类似。

   ![虚拟 WAN 示意图](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。