---
title: VPN 网关：用于 P2S VPN 连接的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: alzam
ms.openlocfilehash: 1f7cf97e38bf201679593819cce814249f9625b0
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930424"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>创建用于 P2S OpenVPN 协议连接的 Azure Active Directory 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，当你使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 本文可帮助你设置用于 P2S Open VPN 身份验证的 Azure AD 租户。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

## <a name="tenant"></a>1. 创建 Azure AD 租户

使用[创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建 Azure AD 租户：

* 组织名称
* 初始域名

示例：

   ![新 Azure AD 租户](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. 创建 Azure AD 租户用户

接下来，创建两个用户帐户。 创建一个全局管理员帐户和一个主用户帐户。 主用户帐户用作您的主嵌入帐户（服务帐户）。 当你创建 Azure AD 租户用户帐户时，将为你要创建的用户类型调整目录角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤，为你的 Azure AD 租户至少创建两个用户。 确保更改**目录角色**以创建帐户类型：

* 全局管理员
* 用户

## <a name="enable-authentication"></a>3. 在 VPN 网关上启用 Azure AD 身份验证

1. 找到要用于身份验证的目录的目录 ID。 它在 "Active Directory" 页的 "属性" 部分中列出。

    ![Directory ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 复制目录 ID。

3. 以分配有**全局管理员**角色的用户身份登录到 Azure 门户。

4. 接下来，请授予管理员许可。 在浏览器的地址栏中复制并粘贴与你的部署位置相关的 URL：

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

5. 如果出现提示，请选择**全局管理员**帐户。

    ![Directory ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 出现提示时，选择 "**接受**"。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在 Azure AD 的 "**企业应用程序**" 中，会看到 " **Azure VPN** " 已列出。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. 按照为[连接到 Azure 的点到站点连接配置 Azure AD 身份验证](virtual-wan-point-to-site-azure-ad.md)中的步骤，为用户 VPN 配置 Azure AD 身份验证，并将其分配给虚拟中心

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件，并将其与虚拟中心相关联。 请参阅[配置 Azure AD 身份验证以连接到 Azure 的点到站点连接](virtual-wan-point-to-site-azure-ad.md)。
