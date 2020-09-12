---
title: 配置 Always-On VPN 用户隧道
titleSuffix: Azure Virtual WAN
description: 本文介绍如何为虚拟 WAN 配置 Always On VPN 用户隧道
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016345"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>为虚拟 WAN 配置 Always On VPN 用户隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>先决条件

必须创建点到站点配置并编辑虚拟中心分配。 有关说明，请参阅以下部分：

* [创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)
* [通过 P2S gateway 创建集线器](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>配置用户隧道

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请执行以下步骤：

1. 运行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 断开连接，清除“自动连接”复选框。 

   ![清理](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
