---
title: 配置 Always-On VPN 隧道
titleSuffix: Azure Virtual WAN
description: 为虚拟 WAN 配置 Always On VPN 设备隧道的步骤
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983677"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>为虚拟 WAN 配置 Always On VPN 设备隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>先决条件

必须创建点到站点配置并编辑虚拟中心分配。 有关说明，请参阅以下部分：

* [创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)
* [通过 P2S gateway 创建集线器](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>配置设备隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请运行以下命令：

![屏幕截图显示运行命令 VpnConnection-Name MachineCertTest 的 PowerShell 窗口。](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。