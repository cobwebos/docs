---
title: 为 VPN 用户启用 MFA： Azure AD 身份验证 |Microsoft Docs
description: 为 VPN 用户启用多重身份验证
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 1b506f8439deabd110daaefab36442140e20d0d4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185355"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>为 VPN 用户启用多重身份验证（MFA）

可以为 Azure AD 租户中的用户启用 MFA，以便在授予访问权限之前提示用户进行第二因素身份验证。

> [!NOTE]
> 先决条件：已根据 "配置租户" 文档中所述配置了 Azure AD 租户。
>

### <a name="tenant"></a>1. 登录到 Azure 门户，导航到**Azure Active Directory** ，**所有用户**，然后单击**多重身份验证**


   ![新 Azure AD 租户](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. 选择要为其启用 MFA 的用户，然后单击 "**启用**"

   ![新 Azure AD 租户](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. 导航到**Azure Active Directory** ，**企业应用程序**，**所有应用程序**，并单击**Azure VPN**


   ![Directory ID](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. 确保 "**已启用用户登录"** 设置为 "是"。 如果只想让具有 Azure VPN 权限的用户能够登录，则将 "**需要进行用户分配"** 设置为 "是"，否则 AD 租户中的所有用户都可以成功连接到 VPN。

   ![权限](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 vpn 客户端以进行 P2S vpn 连接](openvpn-azure-ad-client.md)。
