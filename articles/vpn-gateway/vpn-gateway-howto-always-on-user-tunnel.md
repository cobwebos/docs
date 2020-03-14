---
title: 配置 Always On VPN 用户隧道
titleSuffix: Azure VPN Gateway
description: 本文介绍如何为 VPN 网关配置 Always On VPN 用户隧道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: bf9dbd0cef19ad54ba6c3b58f2b9b3071b98bd93
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370951"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>配置 Always On VPN 用户隧道

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>配置网关

 使用[配置点到站点 vpn 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)一文中的说明，将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>删除配置文件

若要删除配置文件，请执行以下步骤：

1. 运行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 断开连接，并清除 "**自动连接**" 复选框。

   ![清理](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>后续步骤

若要解决可能出现的任何连接问题，请参阅[Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
