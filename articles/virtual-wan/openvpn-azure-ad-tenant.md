---
title: 用于用户 VPN 连接的 Azure AD 租户： Azure AD 身份验证
description: 可使用 Azure 虚拟 WAN 用户 VPN（点到站点）通过 Azure AD 身份验证连接到 VNet
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367846"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>为用户 VPN OpenVPN 协议连接准备 Azure Active Directory 租户

通过 IKEv2 协议连接到虚拟中心时，可使用基于证书的身份验证或 RADIUS 身份验证。 但在使用 OpenVPN 协议时，还可使用 Azure Active Directory 身份验证。 本文可帮助你使用 OpenVPN 身份验证为虚拟 WAN 用户 VPN（点到站点）设置 Azure AD 租户。

> [!NOTE]
> 仅 OpenVPN&reg; 协议连接支持 Azure AD 身份验证。
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.创建 Azure AD 租户

验证你是否有 Azure AD 租户。 如果没有 Azure AD 租户，可以按照[创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建一个：

* 组织名称
* 初始域名

示例：

   ![新 Azure AD 租户](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2.创建 Azure AD 租户用户

接下来，在新建的 Azure AD 租户创建两个用户帐户：一个全局管理员帐户和一个用户帐户。 用户帐户可用于测试 OpenVPN 身份验证，全局管理员帐户将用于向 Azure VPN 应用注册授予许可。 创建 Azure AD 用户帐户后，向用户分配目录角色来委派管理权限。

按照[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤为 Azure AD 租户创建两个用户。 请确保将其中一个已创建的帐户的目录角色更改为“全局管理员” 。

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3.向 Azure VPN 应用注册授予许可

1. 以具有全局管理员角色的用户身份登录到 Azure 门户。

2. 接下来，向组织授予管理员许可，使 Azure VPN 应用程序能够登录和读取用户配置文件。 在浏览器的地址栏中复制并粘贴与部署位置相关的 URL：

    公共

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    德国 Microsoft 云

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure 中国世纪互联

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. 出现提示时，请选择“全局管理员”帐户。

    ![Directory ID](./media/openvpn-create-azure-ad-tenant/pick.png)

4. 出现提示时选择“接受”。

    ![屏幕截图显示具有为你的组织请求接受的消息权限的对话框和其他信息。](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. 在 Azure AD 下的“企业应用程序”中，你现会发现已列出 Azure VPN 。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>后续步骤

若要使用 Azure AD 身份验证连接到虚拟网络，必须创建用户 VPN 配置，并将其与虚拟中心关联。 请参阅[为与 Azure 的点到站点连接配置 Azure AD 身份验证](virtual-wan-point-to-site-azure-ad.md)。
