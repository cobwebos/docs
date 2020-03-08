---
title: VPN 网关：用于 P2S VPN 连接的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: f4092f651a3058c8a2e738c81d9db7e296386bfa
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402895"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>创建用于 P2S OpenVPN 协议连接的 Azure Active Directory 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，当你使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 本文可帮助你设置用于 P2S Open VPN 身份验证的 Azure AD 租户。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>


## <a name="tenant"></a>1. 验证 Azure AD 租户

验证你是否拥有 Azure AD 租户。 如果没有 Azure AD 租户，可以使用[创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建一个租户：

* 组织名称
* 初始域名

示例：

   ![新 Azure AD 租户](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. 创建 Azure AD 租户用户

Azure AD 租户需要以下帐户：全局管理员帐户和主用户帐户。 主用户帐户用作您的主嵌入帐户（服务帐户）。 当你创建 Azure AD 租户用户帐户时，将为你要创建的用户类型调整目录角色。

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
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 如果出现提示，请选择**全局管理员**帐户。

    ![Directory ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 出现提示时，选择 "**接受**"。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. 在 Azure AD 的 "**企业应用程序**" 中，会看到 " **Azure VPN** " 已列出。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. 如果还没有正常运行的“点到站点”环境，请按照说明创建一个。 请参阅[创建点到站点 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，以创建和配置使用本机 Azure 证书身份验证的点到站点 VPN 网关。 

    > [!IMPORTANT]
    > OpenVPN 不支持基本 SKU。

9. 通过运行以下命令在 VPN 网关上启用 Azure AD 身份验证，并确保修改命令以反映自己的环境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > 请确保在 `AadIssuerUri` 值的末尾包含尾随斜杠。 否则，该命令将失败。

10. 通过运行以下命令创建并下载配置文件。 更改-ResourceGroupName 和-Name 值，使其与你自己的值匹配。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. 运行这些命令后，会看到如下所示的结果。 将结果 URL 复制到浏览器以下载配置文件 zip 文件。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. 提取已下载的 zip 文件。

13. 浏览到解压缩的 "Azurevpn.bgpsettings.asn" 文件夹。

14. 记下 "azurevpnconfig" 文件的位置。 Azurevpnconfig 包含 VPN 连接的设置，可以直接导入 Azure VPN 客户端应用程序。 你还可以将此文件分发给需要通过电子邮件或其他方式进行连接的所有用户。 用户将需要有效的 Azure AD 凭据才能成功连接。

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 vpn 客户端以进行 P2S vpn 连接](openvpn-azure-ad-client.md)。
