---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4cef4e09038288e4609f37c4e33f225bd79113be
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166649"
---
## <a name="mfa"></a>打开 MFA 页

1. 登录到 Azure 门户。
2. 导航到**Azure Active Directory-> 所有用户**"。
3. 选择 "**多重身份验证**" 打开 "多重身份验证" 页。

   ![登录](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>选择用户

1. 在 "**多重身份验证**" 页上，选择要为其启用 MFA 的用户。
2. 选择“启用”。

   ![选择](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>启用身份验证

1. 导航到**Azure Active Directory > 企业应用程序-> 所有应用程序**。
2. 在 "**企业应用程序-所有应用程序**" 页上，选择 " **Azure VPN**"。

   ![Directory ID](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)
