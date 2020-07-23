---
title: 用于用户 VPN 连接的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 Azure 虚拟 WAN 用户 VPN （点到站点）通过 Azure AD authentication 连接到 VNet
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: e88437dc03772348ebbe0d179afc7fd4ddd24bd9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507550"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>为用户 VPN OpenVPN 协议连接准备 Azure Active Directory 租户

通过 IKEv2 协议连接到虚拟中心时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，在使用 OpenVPN 协议时，还可以使用 Azure Active Directory 身份验证。 本文介绍如何使用 OpenVPN authentication 为虚拟 WAN 用户 VPN （点到站点）设置 Azure AD 租户。

> [!NOTE]
> 仅 OpenVPN&reg; 协议连接支持 Azure AD 身份验证。
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.创建 Azure AD 租户

验证你是否拥有 Azure AD 租户。 如果没有 Azure AD 租户，可以使用[创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建一个租户：

* 组织名称
* 初始域名

示例：

   ![新 Azure AD 租户](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2.创建 Azure AD 租户用户

接下来，在新创建的 Azure AD 租户、一个全局管理员帐户和一个用户帐户中创建两个用户帐户。 用户帐户可用于测试 OpenVPN authentication，全局管理员帐户将用于向 Azure VPN 应用注册授予许可。 创建 Azure AD 用户帐户后，你将**目录角色**分配给用户，以便委派管理权限。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤为 Azure AD 租户创建两个用户。 请确保将所创建的某个帐户的**目录角色**更改为 "**全局管理员**"。

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. 同意 Azure VPN 应用注册

1. 以分配有**全局管理员**角色的用户身份登录到 Azure 门户。

2. 接下来，向你的组织授予管理员许可，这允许 Azure VPN 应用程序登录和读取用户配置文件。 在浏览器的地址栏中复制并粘贴与你的部署位置相关的 URL：

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

3. 如果出现提示，请选择 "**全局管理员**" 帐户。

    ![Directory ID](./media/openvpn-create-azure-ad-tenant/pick.png)

4. 出现提示时选择“接受”。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. 在 Azure AD 的 "**企业应用程序**" 中，现在应会看到列出的**Azure VPN** 。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>后续步骤

若要使用 Azure AD authentication 连接到虚拟网络，必须创建用户 VPN 配置，并将其与虚拟中心相关联。 请参阅[为与 Azure 的点到站点连接配置 Azure AD 身份验证](virtual-wan-point-to-site-azure-ad.md)。
