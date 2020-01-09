---
title: VPN 网关：不同用户组的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477169"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>创建用于 P2S OpenVPN 协议连接的 Azure Active Directory 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，当你使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 如果希望不同的用户集能够连接到不同的 VPN 网关，可以在 AD 中注册多个应用，并将其链接到不同的 VPN 网关。 本文将帮助你设置用于 P2S OpenVPN authentication 的 Azure AD 租户，并在 Azure AD 中创建和注册多个应用，以便为不同的用户和组提供不同的访问权限。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

## <a name="tenant"></a>1. 创建 Azure AD 租户

使用[创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建 Azure AD 租户：

* 组织名称
* 初始域名

示例：

   ![新 Azure AD 租户](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. 创建 Azure AD 租户用户

接下来，创建两个用户帐户。 创建一个全局管理员帐户和一个主用户帐户。 主用户帐户用作您的主嵌入帐户（服务帐户）。 当你创建 Azure AD 租户用户帐户时，将为你要创建的用户类型调整目录角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤，为你的 Azure AD 租户至少创建两个用户。 确保更改**目录角色**以创建帐户类型：

* 全局管理员
* 用户

## <a name="enable-authentication"></a>3. 在 Azure AD 租户中注册 Azure VPN 客户端

1. 找到要用于身份验证的目录的目录 ID。 它在 "Active Directory" 页的 "属性" 部分中列出。

    ![Directory ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![Directory ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 出现提示时，选择 "**接受**"。

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在 Azure AD 的 "**企业应用程序**" 中，会看到列出**Azure VPN** 。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. 为各种用户/组注册其他应用程序

1. 在 Azure Active Directory 下，依次单击 "**应用注册**" 和 "**新建注册**"

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 在 "**注册应用程序**" 边栏选项卡中输入**名称**，然后选择所需的**受支持帐户类型**，并单击 "**注册**"

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 注册新应用后，请单击 "应用" 边栏选项卡下的 "**公开 API** "

4. 单击 " **+ 添加范围**"
5. 保留默认的 "**应用程序 ID URI** "，然后单击 "**保存并继续**"

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. 填写必填字段，确保**状态**为 "已**启用**"。 单击 "**添加范围**"

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. 单击 "**公开 API** "，然后单击 "**添加客户端应用程序**"。  对于 "**客户端 ID**"，输入以下值，具体取决于云：
    -   输入**41b23e61-6c1e-4545-b367-cd054e0ed4b4** For Azure **Public**
    -   为 Azure**政府**版输入**51bb15d4-3a4f-4ebf-9dca-40096fe32426**
    -   为 Azure**德国**输入**538ee9e6-310a-468d-afef-ea97365856a9**
    -   输入**49f817b6-84ae-4cc0-928c-73f27289b3aa** For Azure**中国世纪互联**


8. 单击 "**添加应用程序**"

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. 从 "**概述**" 页复制**应用程序（客户端） ID** 。 你将需要它来配置 VPN 网关

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 重复本部分中的步骤（4），创建安全要求所需的多个应用程序。 每个应用程序都将关联到一个 VPN 网关，并且可以有不同的用户集。 一个网关只能关联一个应用程序。

## <a name="enable-authentication"></a>5. 将用户分配到应用程序

1. 在 "Azure AD" 下，选择 "**企业应用程序**"，选择新建的应用程序，然后单击 "**属性**"。 请确保 **"需要进行用户分配"** 设置为 **"是"** 。 单击“保存”

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 在应用页上，单击 "**用户和组**"，然后单击 "**添加用户**"

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. 在 "**添加分配**" 下，单击 "**用户和组**"。 选择希望能够访问此 VPN 应用程序的用户。 单击“选择”

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. 在 VPN 网关上启用 Azure AD 身份验证

1. 通过运行以下命令在 VPN 网关上启用 Azure AD 身份验证，并确保修改命令以反映自己的环境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> 在上述命令中，不要使用 Azure VPN 客户端的应用程序 ID，因为它会向所有用户授予对 VPN 网关的访问权限。 使用注册的应用程序的 ID。

2. 通过运行以下命令创建并下载配置文件。 更改-ResourcGroupName 和-Name 值，使其与你自己的值匹配。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. 运行这些命令后，会看到如下所示的结果。 将结果 URL 复制到浏览器以下载配置文件 zip 文件。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. 提取已下载的 zip 文件。

5. 浏览到解压缩的 "Azurevpn.bgpsettings.asn" 文件夹。

6. 记下 "azurevpnconfig" 文件的位置。 Azurevpnconfig 包含 VPN 连接的设置，可以直接导入 Azure VPN 客户端应用程序。 你还可以将此文件分发给需要通过电子邮件或其他方式进行连接的所有用户。 用户将需要有效的 Azure AD 凭据才能成功连接。

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 vpn 客户端以进行 P2S vpn 连接](openvpn-azure-ad-client.md)。
