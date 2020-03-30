---
title: 配置 Always-On VPN 隧道
titleSuffix: Azure VPN Gateway
description: 为 VPN 网关配置 Always On VPN 隧道的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371705"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>配置 Always On VPN 设备隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>配置网关

使用["配置点对点 VPN 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)"一文将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

## <a name="configure-the-device-tunnel"></a>配置设备隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请运行以下命令：

![清理](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

若要进行故障排除，请参阅 [Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
