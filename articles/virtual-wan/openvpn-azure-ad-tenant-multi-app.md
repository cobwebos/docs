---
title: VPN 网关：不同用户组的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985639"
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

## <a name="site"></a>6. 创建新的 P2S 配置

P2S 配置定义连接远程客户端的参数。

1. 设置以下变量（请根据环境替换为所需的值）。

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 运行以下命令创建配置：

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> 在上述命令中，不要使用 Azure VPN 客户端的应用程序 ID，因为它会向所有用户授予对 VPN 网关的访问权限。 使用注册的应用程序的 ID。

## <a name="hub"></a>7. 编辑中心分配

1. 导航到虚拟 WAN 下的“中心”边栏选项卡。
2. 选择要将 VPN 服务器配置关联到的中心，然后单击省略号图标 (...)。

   ![新建站点](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. 单击“编辑虚拟中心”。
4. 选中“包括点到站点网关”复选框，然后选择所需的网关缩放单元。

   ![新建站点](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. 输入用于为 VPN 客户端分配 IP 地址的“地址池”。
6. 单击“确认”。
7. 完成此操作最多需要 30 分钟。

## <a name="device"></a>8. 下载 VPN 配置文件

使用 VPN 配置文件来配置客户端。

1. 在虚拟 WAN 的页面上，单击“用户 VPN 配置”。
2. 在页面顶部，单击“下载用户 VPN 配置”。
3. 完成创建文件后，可以单击相应的链接下载该文件。
4. 使用此配置文件配置 VPN 客户端。

4. 提取已下载的 zip 文件。

5. 浏览到解压缩的 "Azurevpn.bgpsettings.asn" 文件夹。

6. 记下 "azurevpnconfig" 文件的位置。 Azurevpnconfig 包含 VPN 连接的设置，可以直接导入 Azure VPN 客户端应用程序。 你还可以将此文件分发给需要通过电子邮件或其他方式进行连接的所有用户。 用户将需要有效的 Azure AD 凭据才能成功连接。
## <a name="configure-user-vpn-clients"></a>配置用户 VPN 客户端

若要进行连接，需在要连接到 VNet 的每台计算机上，下载 Azure VPN 客户端（预览版）并导入在前面步骤中下载的 VPN 客户端配置文件。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

#### <a name="to-download-the-azure-vpn-client"></a>下载 Azure VPN 客户端

使用[此链接](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)下载 Azure VPN 客户端（预览版）。

#### <a name="import"></a>导入客户端配置文件

1. 在页面上，选择“导入”。

    ![导入](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。

    ![导入](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. 指定配置文件的名称，并选择“保存”。

    ![导入](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. 选择“连接”以连接到 VPN。

    ![导入](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 连接后，图标将变为绿色并指示“已连接”。

    ![导入](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号图标 (...)。 然后选择“删除”。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. 选择“删除”以删除配置文件。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用“诊断”工具。 选择要诊断的 VPN 连接旁边的省略号图标 (...) 以显示菜单。 然后选择“诊断”。

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. 在“连接属性”页上，选择“运行诊断”。

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 使用凭据登录。

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![诊断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>查看资源运行状况

1. 导航到 WAN。
2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。


## <a name="cleanup"></a>清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
