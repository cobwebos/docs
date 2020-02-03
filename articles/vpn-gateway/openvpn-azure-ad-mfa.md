---
title: 为 VPN 用户启用 MFA： Azure AD 身份验证
description: 为 VPN 用户启用多重身份验证
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964684"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>为 VPN 用户启用 Azure 多重身份验证（MFA）

如果你希望在授予访问权限之前系统提示用户提供第二重身份验证，则可以基于每个用户配置 Azure 多重身份验证（MFA），或利用多重身份验证（MFA）通过[条件访问](../active-directory/conditional-access/overview.md)进行更精细的控制。 可以免费启用每个用户的多重身份验证，但在按用户启用 MFA 时，系统将提示用户对绑定到 Azure AD 租户的所有应用程序进行第二因素身份验证。 通过条件性访问，可以更精细地控制第二个因素的升级方式，并且可以允许将 MFA 分配到仅 VPN，而不是其他与 Azure AD 租户关联的应用程序。

## <a name="enableauth"></a>启用身份验证

1. 导航到**Azure Active Directory > 企业应用程序-> 所有应用程序**。
2. 在 "**企业应用程序-所有应用程序**" 页上，选择 " **Azure VPN**"。

   ![Directory ID](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>配置登录设置

在 " **AZURE VPN-属性**" 页上，配置登录设置。

1. **是否将 "已启用以便用户登录？** " 设置为 **"是"** 。 这允许 AD 租户中的所有用户成功连接到 VPN。
2. 是否要将 "**用户分配"** 设置为 **"是"** ，以将登录限制为仅对 Azure VPN 具有权限的用户。
3. 保存所做更改。

   ![权限](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>选项 1-通过条件访问启用多重身份验证（MFA）

条件性访问允许基于每个应用程序进行细化的访问控制。  请注意，若要利用条件性访问，应将 Azure AD Premium 1 个或更高的许可应用于将受条件性访问规则制约的用户。

1. 在 "**企业应用程序-所有应用程序**" 页上，选择 " **Azure VPN**"，选择 "**条件访问**"，然后单击 "**新建策略**"。
2. 在 "用户和组" 下，在 "*包括*" 选项卡上，**选中 "用户和**组"，选中 "**用户和组**"，然后选择一个组或一组应进行 MFA 的用户。  单击“完成”。
![分配](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. 在 "**授权**" 下，选中 "**授予访问权限**"、"检查**需要多重身份验证**"、"检查**要求所有选定的控件**"，然后单击 "**选择**" 按钮。
![授予访问权限-MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. 在 "**启用策略** **" 下查看**，然后单击 "**创建**" 按钮。
![启用策略](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>选项 2-启用每个用户的多重身份验证（MFA）

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 vpn 客户端以进行 P2S vpn 连接](openvpn-azure-ad-client.md)。
