---
title: VPN 网关：用于 P2S OpenVPN 协议连接的 VPN 客户端：Azure AD 身份验证
description: 了解如何使用点到站点 VPN 和 Azure Active Directory 身份验证配置 VPN 客户端以连接到虚拟网络。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 51004005e10416f3138f69f91b93b9cd19ed3944
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819754"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory 身份验证：配置用于 P2S OpenVPN 协议连接的 VPN 客户端

本文帮助你配置 VPN 客户端，以使用点到站点 VPN 和 Azure Active Directory 身份验证连接到虚拟网络。 在使用 Azure AD 进行连接和身份验证之前，必须先配置 Azure AD 租户。 有关详细信息，请参阅[配置 Azure AD 租户](openvpn-azure-ad-tenant.md)。

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>使用客户端配置文件

若要进行连接，需要下载 Azure VPN 客户端，并在要连接到 VNet 的每台计算机上配置 VPN 客户端配置文件。 可以在计算机上创建客户端配置文件，导出该配置文件，然后将其导入到其他计算机。

### <a name="to-download-the-azure-vpn-client"></a>下载 Azure VPN 客户端

使用[此链接](https://go.microsoft.com/fwlink/?linkid=2117554)下载 Azure VPN 客户端。 请确保 Azure VPN 客户端有权在后台运行。 若要检查/启用权限，请执行以下步骤：

1. 转到“开始”，然后选择“设置”>“隐私”>“后台应用”。
2. 在“后台应用”下，确保将“允许应用在后台运行”切换到“开”。 
3. 在“选择可在后台运行的应用”下，将 Azure VPN 客户端的设置转换为“开”  。

  ![权限 (permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>创建基于证书的客户端配置文件

使用基于证书的配置文件时，请确保已在客户端计算机上安装适当的证书。 有关证书的详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>创建 RADIUS 客户端配置文件

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> 可以在 P2S VPN 客户端配置文件中导出服务器机密。  可在[此处](about-vpn-profile-download.md)找到有关如何导出客户端配置文件的说明。
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>导出和分发客户端配置文件

创建有效的配置文件后，如果需要将其分发给其他用户，可使用以下步骤将其导出：

1. 突出显示要导出的 VPN 客户端配置文件，然后依次选择“...”、“导出”。  

    ![显示 "Azure VPN 客户端" 页的屏幕截图，其中选中省略号并突出显示 "导出"。](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 选择要将此配置文件保存到的位置，保留默认的文件名，然后选择“保存”以保存 xml 文件。 

    ![导出](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>导入客户端配置文件

1. 在页面上，选择“导入”。 

    ![屏幕截图，显示所选的 "添加" 按钮和窗口左下方突出显示的 "导入" 操作。](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。 

    ![显示所选配置文件 x m l 文件的屏幕截图。](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 指定配置文件的名称，并选择“保存”。 

    ![显示 "连接名称" 并选择 "保存" 按钮的屏幕截图。](./media/openvpn-azure-ad-client/import/import3.jpg)

4. 选择“连接”以连接到 VPN。 

    ![显示 VPN 和所选 "连接" 按钮的屏幕截图。](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 连接后，图标将变为绿色并指示“已连接”。 

    ![进口](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号图标。 然后选择“删除”  。

    ![显示所选省略号和 "删除" 选项的屏幕截图。](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. 选择“删除”以删除配置文件。 

    ![删除](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>创建连接

1. 在页面上，依次选择 **+** 、“+ 添加”。 

    ![显示选定 "添加" 按钮的屏幕截图。](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 填写连接信息。 如果你不确定要输入哪些值，请与管理员联系。 填写值后，选择“保存”。 

    ![显示 VPN 连接属性的屏幕截图，并选中 "保存" 按钮。](./media/openvpn-azure-ad-client/create/create2.jpg)

3. 选择“连接”以连接到 VPN。 

    ![显示选定的 "连接" 按钮的屏幕截图。](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 选择正确的凭据，然后选择“继续”。 

    ![屏幕截图，显示突出显示的示例凭据并选中 "继续" 按钮。](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 成功连接后，图标将变为绿色并指示“已连接”。 

    ![连接](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>自动连接

以下步骤帮助你配置自动连接并始终保持连接。

1. 在 VPN 客户端的主页上，选择“VPN 设置”。 

    ![已选择 "VPN 设置" 的 VPN 主页的屏幕截图。](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 在切换应用对话框中选择“是”。 

    !["你想要切换应用程序吗？" 屏幕截图 选中了 "是" 按钮的对话框。](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 请确保要设置的连接尚未建立连接，然后突出显示该配置文件并选中“自动连接”复选框。 

    !["设置" 窗口的屏幕截图，选中 "自动连接" 框。](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. 选择“连接”启动 VPN 连接。 

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用“诊断”工具。  选择要诊断的 VPN 连接旁边的“...”以显示菜单。  然后选择“诊断”。 

    ![省略号和 "诊断选定" 的屏幕截图。](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. 在“连接属性”页上，选择“运行诊断”。  

    ![显示 "连接属性" 页的屏幕截图，其中选择了 "运行诊断"。](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 使用凭据登录。

    ![屏幕截图显示 "让你登录" 对话框，其中选择了 "工作或学校帐户"。](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![诊断](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>常见问题

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>如何将 DNS 后缀添加到 VPN 客户端？

可以修改下载的配置文件 XML 文件并添加 \<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes> 标记

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>如何将自定义 DNS 服务器添加到 VPN 客户端？

可以修改下载的配置文件 XML 文件并添加 \<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers> 标记

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD 客户端使用 DNS 名称解析策略表 (NRPT) 条目，这意味着不会在 `ipconfig /all` 的输出下列出 DNS 服务器。 若要确认使用中的 DNS 设置，请使用 PowerShell 中的 [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps&preserve-view=true)。
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>如何将自定义路由添加到 VPN 客户端？

可以修改下载的配置文件 XML 文件并添加 \<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes> 标记

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>如何在 VPN 客户端中阻止（排除）路由？

可以修改下载的配置文件 XML 文件并添加 \<excluderoutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes> 标记

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>是否可以从命令行提示符导入配置文件？

可以从命令行提示符导入配置文件，方法是将下载的 azurevpnconfig.xml  文件放在“%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState”  文件夹中，并运行以下命令：

```
azurevpn -i azurevpnconfig.xml 
```
若要强制导入，还应使用“-f”  开关


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[为使用 Azure AD 身份验证的 P2S 开放 VPN 连接创建 Azure Active Directory 租户](openvpn-azure-ad-tenant.md)。
