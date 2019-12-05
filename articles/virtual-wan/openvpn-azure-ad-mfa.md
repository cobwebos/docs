---
title: 为 VPN 用户启用 MFA： Azure AD 身份验证
description: 为 VPN 用户启用多重身份验证
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 3700014752233e29d157f1695906babe4fa35c74
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822768"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>为 VPN 用户启用 Azure 多重身份验证（MFA）

如果希望在授予访问权限之前提示用户提供第二个身份验证因素，可以为 Azure AD 租户配置 Azure 多重身份验证（MFA）。 本文中的步骤可帮助你为双重验证启用要求。

## <a name="prereq"></a>先决条件

此配置的先决条件是使用[配置租户](openvpn-azure-ad-tenant.md)中的步骤配置 Azure AD 租户。

## <a name="mfa"></a>打开 MFA 页

1. 登录到 Azure 门户。
2. 导航到**Azure Active Directory-> 所有用户**"。
3. 选择 "**多重身份验证**" 打开 "多重身份验证" 页。

   ![登录](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>选择用户

1. 在 "**多重身份验证**" 页上，选择要为其启用 MFA 的用户。
2. 选择“启用”。

   ![Select](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>启用身份验证

1. 导航到**Azure Active Directory > 企业应用程序-> 所有应用程序**。
2. 在 "**企业应用程序-所有应用程序**" 页上，选择 " **Azure VPN**"。

   ![Directory ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>配置登录设置

在 " **AZURE VPN-属性**" 页上，配置登录设置。

1. **是否将 "已启用以便用户登录？** " 设置为 **"是"** 。 这允许 AD 租户中的所有用户成功连接到 VPN。
2. 是否要将 "**用户分配"** 设置为 **"是"** ，以将登录限制为仅对 Azure VPN 具有权限的用户。
3. 保存所做更改。

   ![权限](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 Azure AD 身份验证以连接到 Azure 的点到站点连接](virtual-wan-point-to-site-azure-ad.md)。
