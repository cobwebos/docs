---
title: VPN 网关：用于 OpenVPN 协议 P2S 连接的 VPN 客户端： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: alzam
ms.openlocfilehash: 3072fc3a82cfe85649cf080c9def69cc4869b7ec
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122501"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>为 P2S OpenVPN 协议连接配置 VPN 客户端： Azure AD 身份验证

本文介绍如何使用点到站点 VPN 和 Azure Active Directory 身份验证配置 VPN 客户端以连接到虚拟网络。 必须先配置 Azure AD 租户，然后才能使用 Azure AD 进行连接和身份验证。 有关详细信息，请参阅[Configure a Azure AD 租户](openvpn-azure-ad-tenant.md)。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

## <a name="profile"></a>使用客户端配置文件

若要连接，需要下载 Azure VPN 客户端，并在要连接到 VNet 的每台计算机上配置 VPN 客户端配置文件。 你可以在计算机上创建客户端配置文件，将其导出，然后将其导入到其他计算机。

### <a name="to-download-the-azure-vpn-client"></a>下载 Azure VPN 客户端

使用此[链接](https://go.microsoft.com/fwlink/?linkid=2117554)下载 Azure VPN 客户端。

### <a name="cert"></a>创建基于证书的客户端配置文件

使用基于证书的配置文件时，请确保在客户端计算机上安装了相应的证书。 有关证书的详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>创建 RADIUS 客户端配置文件

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> 服务器机密可以在 P2S VPN 客户端配置文件中导出。  有关如何导出客户端配置文件的说明，请参阅[此处](about-vpn-profile-download.md)。
>

### <a name="export"></a>导出和分发客户端配置文件

获得工作配置文件并需要将其分发给其他用户后，可以使用以下步骤将其导出：

1. 突出显示要导出的 VPN 客户端配置文件，选择 " **...** "，然后选择 "**导出**"。

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 选择要将此配置文件保存到的位置，将 "文件名" 保留原样，然后选择 "**保存**" 以保存 xml 文件。

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>导入客户端配置文件

1. 在页面上，选择“导入”。

    ![导入](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。

    ![导入](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 指定配置文件的名称，并选择“保存”。

    ![导入](./media/openvpn-azure-ad-client/import/import3.jpg)

4. 选择“连接”以连接到 VPN。

    ![导入](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 连接后，图标将变为绿色并指示“已连接”。

    ![导入](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号。 然后选择“删除”。

    ![删除](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. 选择“删除”以删除配置文件。

    ![删除](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>创建连接

1. 在 "" 页上，选择 " **+** "，然后单击 " **+ 添加**"。

    ![connection](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 填写连接信息。 如果你不确定这些值，请与你的管理员联系。 填写值后，选择 "**保存**"。

    ![connection](./media/openvpn-azure-ad-client/create/create2.jpg)

3. 选择“连接”以连接到 VPN。

    ![connection](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 选择正确的凭据，并选择 "**继续**"。

    ![connection](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 成功连接后，该图标将变为绿色，并指示**已连接**。

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>自动连接

这些步骤可帮助你将连接配置为通过 Always on 自动连接。

1. 在 VPN 客户端的主页上，选择 " **VPN 设置**"。

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 在 "切换应用" 对话框中选择 **"是"** 。

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 请确保要设置的连接尚未连接，然后突出显示该配置文件并选中 "**自动连接**" 复选框。

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. 选择 "**连接**" 以启动 VPN 连接。

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用“诊断”工具。 选择要诊断的 VPN 连接旁的 " **...** "，以显示菜单。 然后选择“诊断”。

    ![诊断](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. 在“连接属性”页上，选择“运行诊断”。

    ![诊断](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 使用凭据登录。

    ![诊断](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![诊断](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>常见问题

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>如何实现将 DNS 后缀添加到 VPN 客户端？

可以修改下载的配置文件 XML 文件，并添加 **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >** 标记

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>如何实现将自定义路由添加到 VPN 客户端？

您可以修改下载的配置文件 XML 文件，并添加 **\<路由 >\<includeroutes >\<目标 >\<掩码 > \<>\<>/includeroutes\<>/destionation\<>/mask**标记

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[创建用于 P2S 的 Azure Active Directory 租户打开使用 Azure AD 身份验证的 VPN 连接](openvpn-azure-ad-tenant.md)。
