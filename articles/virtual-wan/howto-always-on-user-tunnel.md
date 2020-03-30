---
title: 配置 Always-On VPN 用户隧道
titleSuffix: Azure Virtual WAN
description: 本文介绍如何为虚拟 WAN 配置始终处于 VPN 的用户隧道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502862"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>为虚拟 WAN 配置始终处于 VPN 用户通道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>先决条件

您必须创建点对点配置并编辑虚拟中心分配。 有关说明，请参阅以下部分：

* [创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)
* [编辑中心分配](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>配置用户隧道

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

要删除配置文件，请使用以下步骤：

1. 运行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 断开连接，清除“自动连接”复选框。****

   ![清理](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
