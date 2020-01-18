---
title: 为 VPN 用户启用 MFA： Azure AD 身份验证
description: 为 VPN 用户启用多重身份验证
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169713"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>为 VPN 用户启用 Azure 多重身份验证（MFA）

如果希望在授予访问权限之前提示用户提供第二个身份验证因素，可以为 Azure AD 租户配置 Azure 多重身份验证（MFA）。 本文中的步骤可帮助你为双重验证启用要求。

## <a name="prereq"></a>先决条件

此配置的先决条件是使用[配置租户](openvpn-azure-ad-tenant.md)中的步骤配置 Azure AD 租户。

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>配置登录设置

在 " **AZURE VPN-属性**" 页上，配置登录设置。

1. **是否将 "已启用以便用户登录？** " 设置为 **"是"** 。 这允许 AD 租户中的所有用户成功连接到 VPN。
2. 是否要将 "**用户分配"** 设置为 **"是"** ，以将登录限制为仅对 Azure VPN 具有权限的用户。
3. 保存所做更改。

   ![权限](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 vpn 客户端以进行 P2S vpn 连接](openvpn-azure-ad-client.md)。
