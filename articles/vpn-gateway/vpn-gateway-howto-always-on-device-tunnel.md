---
title: 配置 Always-On VPN 隧道
titleSuffix: Azure VPN Gateway
description: 了解如何在 Windows 10 Always On 中使用网关来建立和配置到 Azure 的永久性设备隧道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7688e42175b2b4e35b63979f5df25702f3bb869d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986598"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>配置 Always On VPN 设备隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>配置网关

请按照[配置点到站点 VPN 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)一文将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

## <a name="configure-the-device-tunnel"></a>配置设备隧道

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请运行以下命令：

![屏幕截图显示运行命令 Remove-VpnConnection 名称 MachineCertTest 的 PowerShell 窗口。](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

若要进行故障排除，请参阅 [Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
