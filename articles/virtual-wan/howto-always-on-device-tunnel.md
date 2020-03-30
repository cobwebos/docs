---
title: 配置 Always-On VPN 隧道
titleSuffix: Azure Virtual WAN
description: 为虚拟 WAN 配置始终位于 VPN 设备隧道的步骤
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502849"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>为虚拟 WAN 配置始终位于 VPN 上的设备隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>先决条件

您必须创建点对点配置并编辑虚拟中心分配。 有关说明，请参阅以下部分：

* [创建 P2S 配置](virtual-wan-point-to-site-portal.md#p2sconfig)
* [编辑中心分配](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>配置设备隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请运行以下命令：

![清理](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。