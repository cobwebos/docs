---
title: VPN 网关：不同用户组的 Azure AD 租户：Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD 身份验证连接到 VNet
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819672"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>为 P2S OpenVPN 协议连接创建 Active Directory (AD) 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，在使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 如果你希望一组不同的用户能够连接到不同的 VPN 网关，可以在 AD 中注册多个应用，并将这些应用链接到不同的 VPN 网关。 本文帮助你设置用于 P2S OpenVPN 身份验证的 Azure AD 租户，并在 Azure AD 中创建和注册多个应用，使不同的用户和组能够以不同的方式进行访问。

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6.在网关上启用身份验证

在此步骤中，将在 VPN 网关上启用 Azure AD 身份验证。

1. 通过导航到“点到站点配置”并选取“OpenVPN (SSL)”作为“隧道类型”，在 VPN 网关上启用 Azure AD 身份验证。 选择“Azure Active Directory”作为“身份验证类型”，然后在“Azure Active Directory”部分填写信息。

    ![Azure 门户视图](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > 请不要使用 Azure VPN 客户端的应用程序 ID：它会向所有用户授予对 VPN 网关的访问权限。 使用注册的应用程序的 ID。

2. 通过单击“下载 VPN 客户端”链接来创建和下载配置文件。

3. 解压缩已下载的 zip 文件。

4. 浏览到解压缩后的“AzureVPN”文件夹。

5. 记下“azurevpnconfig.xml”文件的位置。 azurevpnconfig.xml 包含 VPN 连接的设置，可以直接导入到 Azure VPN 客户端应用程序中。 还可以将此文件分发给需要通过电子邮件或其他方式建立连接的所有用户。 用户需有有效的 Azure AD 凭据才能成功建立连接。

## <a name="next-steps"></a>后续步骤

若要连接到虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 VPN 客户端以建立 P2S VPN 连接](openvpn-azure-ad-client.md)。
