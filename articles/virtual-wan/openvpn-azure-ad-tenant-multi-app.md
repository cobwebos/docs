---
title: 虚拟 WAN：不同用户组的 Azure AD 租户： Azure AD 身份验证
description: 可以使用 P2S VPN 通过 Azure AD authentication 连接到 VNet
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 4e667dcccd612873e8633c375bc3ce611e11c962
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485869"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>创建用于 P2S OpenVPN 协议连接的 Azure Active Directory 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，当你使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 如果希望不同的用户集能够连接到不同的 VPN 网关，可以在 AD 中注册多个应用，并将其链接到不同的 VPN 网关。

本文将帮助你设置用于 P2S OpenVPN authentication 的 Azure AD 租户，并在 Azure AD 中创建和注册多个应用，以允许不同用户和组的不同访问权限。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="site"></a>6. 创建新的 P2S 配置

P2S 配置定义连接远程客户端的参数。

1. 设置以下变量（请根据环境替换为所需的值）。

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 运行以下命令创建配置：

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > 在上述命令中不要使用 Azure VPN 客户端的应用程序 ID：它将授予所有用户对 VPN 网关的访问权限。 使用注册的应用程序的 ID。

## <a name="hub"></a>7. 编辑中心分配

1. 导航到虚拟 WAN 下的“中心”边栏选项卡。

2. 选择要将 VPN 服务器配置关联到的中心，然后单击省略号图标 (...)。

    ![新建站点](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. 单击“编辑虚拟中心”。

4. 选中“包括点到站点网关”复选框，然后选择所需的网关缩放单元。

    ![新建站点](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. 输入用于为 VPN 客户端分配 IP 地址的“地址池”。

6. 单击“确认”。

7. 此操作可能最多需要 30 分钟才能完成。

## <a name="device"></a>8. 下载 VPN 配置文件

使用 VPN 配置文件来配置客户端。

1. 在虚拟 WAN 的页面上，单击“用户 VPN 配置”。

2. 在页面顶部，单击“下载用户 VPN 配置”。

3. 完成创建文件后，可以单击相应的链接下载该文件。

4. 使用此配置文件配置 VPN 客户端。

5. 提取已下载的 zip 文件。

6. 浏览到解压缩的 "Azurevpn.bgpsettings.asn" 文件夹。

7. 记下 "azurevpnconfig" 文件的位置。 Azurevpnconfig 包含 VPN 连接的设置，可以直接导入 Azure VPN 客户端应用程序。 你还可以将此文件分发给需要通过电子邮件或其他方式进行连接的所有用户。 用户将需要有效的 Azure AD 凭据才能成功连接。

## <a name="9-configure-user-vpn-clients"></a>9. 配置用户 VPN 客户端

若要连接，需要下载 Azure VPN 客户端，并导入在要连接到 VNet 的每台计算机上前面步骤中下载的 VPN 客户端配置文件。

> [!NOTE]
> Azure AD 身份验证仅支持用于 OpenVPN®协议连接。
>

#### <a name="to-download-the-azure-vpn-client"></a>下载 Azure VPN 客户端

使用此[链接](https://go.microsoft.com/fwlink/?linkid=2117554)下载 Azure VPN 客户端。

#### <a name="import"></a>导入客户端配置文件

1. 在页面上，选择“导入”。

    ![进口](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. 浏览到 XML 配置文件并将其选中。 选择该文件后，选择“打开”。

    ![进口](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. 指定配置文件的名称，并选择“保存”。

    ![进口](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. 选择“连接”以连接到 VPN。

    ![进口](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. 连接后，图标将变为绿色并指示“已连接”。

    ![进口](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="delete"></a>删除客户端配置文件

1. 选择要删除的客户端配置文件旁边的省略号图标 (...)。 然后选择“删除”。

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. 选择“删除”以删除配置文件。

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="diagnose"></a>诊断连接问题

1. 若要诊断连接问题，可以使用“诊断”工具。 选择要诊断的 VPN 连接旁边的省略号图标 (...) 以显示菜单。 然后选择“诊断”。

    ![诊断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. 在“连接属性”页上，选择“运行诊断”。

    ![诊断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. 使用凭据登录。

    ![诊断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. 查看诊断结果。

    ![诊断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>10. 查看虚拟 WAN

1. 导航到虚拟 WAN。

2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。

3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>11. 查看资源运行状况

1. 导航到 WAN。

2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。

## <a name="cleanup"></a>清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
