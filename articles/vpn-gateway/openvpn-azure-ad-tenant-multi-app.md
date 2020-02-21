---
title: VPN 网关：不同用户组的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485572"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>创建用于 P2S OpenVPN 协议连接的 Azure Active Directory 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，当你使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 如果希望不同的用户集能够连接到不同的 VPN 网关，可以在 AD 中注册多个应用，并将其链接到不同的 VPN 网关。 本文将帮助你设置用于 P2S OpenVPN authentication 的 Azure AD 租户，并在 Azure AD 中创建和注册多个应用，以便为不同的用户和组提供不同的访问权限。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="enable-authentication"></a>6. 在网关上启用身份验证

在此步骤中，将在 VPN 网关上启用 Azure AD 身份验证。

1. 通过运行以下命令，在 VPN 网关上启用 Azure AD 身份验证。 请确保修改这些命令以反映自己的环境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > 在上述命令中不要使用 Azure VPN 客户端的应用程序 ID：它将授予所有用户对 VPN 网关的访问权限。 使用注册的应用程序的 ID。

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
