---
title: "创建并安装适用于 P2S RADIUS 连接的 VPN 客户端配置文件：PowerShell：Azure | Microsoft Docs"
description: "本文介绍如何创建适用于点到站点连接的 VPN 客户端配置文件，此类连接使用 RADIUS 身份验证。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: cherylmc
ms.openlocfilehash: 838065287279f1c17e7f294bc919c4a0421e2a58
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>创建并安装适用于 P2S RADIUS 身份验证的 VPN 客户端配置文件

VPN 客户端配置文件包含在一个 zip 文件中。 配置文件提供的设置是本机 Windows 或 Mac IKEv2 VPN 客户端通过点到站点方式连接到 VNet 所必需的。 RADIUS 服务器提供了多个身份验证选项，因此，VPN 客户端配置因选项而异。

### <a name="workflow"></a>工作流

  1. [设置适用于 P2S 连接的 Azure VPN 网关](point-to-site-how-to-radius-ps.md)。
  2. [设置适用于身份验证的 RADIUS 服务器](point-to-site-how-to-radius-ps.md#radius)。 
  3. （本文）- 获取适用于所选身份验证选项的 VPN 客户端配置，用其在 Windows 设备上设置 VPN 客户端。 这样即可通过 P2S 连接连接到 Azure VNet。
  4. [完成 P2S 配置和连接](point-to-site-how-to-radius-ps.md)。

>[!IMPORTANT]
>如果在生成 VPN 客户端配置文件后，点到站点 VPN 配置（例如 VPN 协议类型或身份验证类型）发生了变化，则必须生成新的 VPN 客户端配置并将其安装在用户设备上。
>
>

## <a name="adeap"></a>关于用户名/密码身份验证

* AD 身份验证：常见的方案是 AD 域身份验证。 在此方案中，用户使用其域凭据连接到 Azure VNet。 可以创建适用于 RADIUS AD 身份验证的 VPN 客户端配置文件。

* 无 AD 的身份验证：也可以配置无 AD 的用户名/密码 RADIUS 身份验证方案。

请确保所有进行连接的用户的用户名/密码凭据可以通过 RADIUS 进行身份验证。 只能针对 EAP-MSCHAPv2 用户名/密码身份验证协议创建配置。 “-AuthenticationMethod”指定为“EapMSChapv2”。

## <a name="usernamefiles"></a> 1.生成 VPN 客户端配置文件

生成 VPN 客户端配置文件来用于用户名/密码身份验证。 可以使用以下命令生成 VPN 客户端配置文件：

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
运行该命令将返回一个链接。 请将该链接复制并粘贴到 Web 浏览器，下载“VpnClientConfiguration.zip”。 解压缩该文件，查看以下文件夹： 
 
* WindowsAmd64 和 WindowsX86 - 这些文件夹分别包含 Windows 64 位和 32 位安装程序包。 
* 泛型 - 此文件夹包含的常规信息用于创建你自己的 VPN 客户端配置。 配置用户名/密码身份验证不需要此文件夹。
* Mac - 如果已在创建虚拟网关时配置了 IKEv2，则会看到名为“Mac”的文件夹，其中包含 mobileconfig 文件。 该文件用于配置 Mac 客户端。

如果已创建客户端配置文件，可以使用“Get-AzureRmVpnClientConfiguration”cmdlet 检索这些文件。 但是，如果对 P2S VPN 配置（例如 VPN 协议类型或身份验证类型）进行更改，该配置不会自动更新。 必须运行“New-AzureRmVpnClientConfiguration”cmdlet 才能创建新的配置下载。

若要检索以前生成的客户端配置文件，请使用以下命令：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

## <a name="setupusername"></a> 2.配置 Windows 和 Mac VPN 客户端
 
### <a name="adwincli"></a>设置 Windows VPN 客户端

只要版本与 Windows 客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统的列表，请参阅[常见问题解答](vpn-gateway-vpn-faq.md#P2S)中的“点到站点”部分。

请使用以下步骤配置适用于证书身份验证的本机 Windows VPN 客户端：

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。 
2. 双击所需的包进行安装。 如果显示 SmartScreen 弹出窗口，请单击“更多信息”，并单击“仍要运行”。
3. 在客户端计算机上，导航到“网络设置”，并单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。 

### <a name="admaccli"></a>设置 Mac (OSX) VPN 客户端

1. 选择 VpnClientSetup.mobileconfig 文件，将其发送给每个用户。 可以通过电子邮件或其他方法执行此操作。

2. 在 Mac 中查找 mobileconfig 文件。

  ![查找 mobilconfig 文件](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. 双击要安装的配置文件，然后单击“继续”。 配置文件名称与 VNet 名称相同。

  ![安装](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. 如果信任配置文件的发送者，请单击“继续”，继续进行安装。

  ![继续](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. 在配置文件安装过程中，可以指定用于 VPN 身份验证的用户名和密码。 此信息不是必须输入的。 此信息在指定后会进行保存，在启动连接时自动使用。 单击“安装”继续。

  ![设置](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. 输入用户名和密码，以便获取在计算机上安装该配置文件所需的必要权限。 单击“确定”。

  ![用户名和密码](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. 安装后，该配置文件即可在“配置文件”对话框中查看。 该对话框也可在以后从“系统首选项”打开。

  ![系统首选项](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. 若要访问 VPN 连接，请从“系统首选项”打开“网络”对话框。

  ![网络](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN 连接显示为“IkeV2-VPN”。 该名称可以通过更新 mobileconfig 文件来更改。

  ![连接](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. 单击“身份验证设置”。 在下拉列表中选择“用户名”，然后输入凭据。 如果此前已输入凭据，系统会在下拉列表中自动选择“用户名”，并预填充用户名和密码。 单击“确定”保存设置。 此时会回到“网络”对话框。

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. 单击“应用”保存所做的更改。 若要启动连接，请单击“连接”。

## <a name="certeap"></a>关于证书身份验证
 
可以创建适用于 RADIUS 证书身份验证的 VPN 客户端配置文件，该身份验证使用 EAP-TLS 协议。 通常情况下，企业颁发的证书用于对 VPN 用户进行身份验证。 请确保所有进行连接的用户均已在其设备上安装证书，且该证书可以通过 RADIUS 服务器进行验证。
 
* “-AuthenticationMethod”为“EapTls”。
* 在证书身份验证过程中，客户端通过验证 RADIUS 服务器的证书完成该服务器的验证。 -RadiusRootCert 为 .cer 文件，其中包含的根证书用于验证 RADIUS 服务器。  
* 有时候，一个 Windows 设备有多个客户端证书。 在身份验证过程中，这会导致系统弹出一个对话框，其中列出所有证书。 然后，用户必须选择要使用的证书。 可以通过指定客户端证书应链接到的根证书，筛选出正确的证书。 “-ClientRootCert”是包含该根证书的 .cer 文件。它是一个可选参数。 如果要从其进行连接的设备只有一个客户端证书，则不需指定此参数。

## <a name="certfiles"></a>1.生成 VPN 客户端配置文件

生成 VPN 客户端配置文件来用于证书身份验证。 可以使用以下命令生成 VPN 客户端配置文件：
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

运行该命令将返回一个链接。 请将该链接复制并粘贴到 Web 浏览器，下载“VpnClientConfiguration.zip”。 解压缩该文件，查看以下文件夹：

* WindowsAmd64 和 WindowsX86 - 这些文件夹分别包含 Windows 64 位和 32 位安装程序包。 
* GenericDevice - 此文件夹包含的常规信息用于创建你自己的 VPN 客户端配置。

如果已创建客户端配置文件，可以使用“Get-AzureRmVpnClientConfiguration”cmdlet 检索这些文件。 但是，如果对 P2S VPN 配置（例如 VPN 协议类型或身份验证类型）进行更改，该配置不会自动更新。 必须运行“New-AzureRmVpnClientConfiguration”cmdlet 才能创建新的配置下载。

若要检索以前生成的客户端配置文件，请使用以下命令：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
## <a name="setupusername"></a> 2.配置 Windows 和 Mac VPN 客户端

### <a name="certwincli"></a>设置 Windows VPN 客户端

1. 选择一个配置包，将其安装在客户端设备上。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。 如果显示 SmartScreen 弹出窗口，请单击“更多信息”，并单击“仍要运行”。 也可将要安装的包保存在其他客户端计算机上。
2. 在客户端计算机上，导航到“网络设置”，并单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。

### <a name="certmaccli"></a>设置 Mac (OSX) VPN 客户端

必须为每个连接到 Azure VNet 的 Mac 设备创建单独的配置文件， 因为这些设备要求在配置文件中指定用于身份验证的用户证书。 Generic 文件夹包含创建配置文件所需的全部信息。

  * VpnSettings.xml 包含服务器地址和隧道类型等重要设置。
  * VpnServerRoot.cer 包含在 P2S 连接设置过程中验证 VPN 网关所需的根证书。
  * RadiusServerRoot.cer 包含在身份验证过程中验证 RADIUS 服务器所需的根证书。

请使用以下步骤在 Mac 中配置适用于证书身份验证的本机 VPN 客户端：

1. 将 VpnServerRoot 和 RadiusServerRoot 根证书导入到 Mac 中。 为此，可将文件复制到 Mac，然后双击它。  
单击“添加”进行导入。

  添加 VpnServerRoot

  ![添加证书](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  添加 RadiusServerRoot

  ![添加证书](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. 在“网络首选项”下打开“网络”对话框，单击“+”创建新的 VPN 客户端连接配置文件，以便通过 P2S 连接来与 Azure VNet 建立连接。

  “接口”值为“VPN”，“VPN 类型”值为“IKEv2”。 在“服务名称”字段中为配置文件指定一个名称，单击“创建”创建 VPN 客户端连接配置文件。

  ![网络](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. 从 **Generic** 文件夹中的 **VpnSettings.xml** 文件复制 **VpnServer** 标记值。 将该值粘贴到配置文件的“服务器地址”和“远程 ID”字段中。 保留“本地 ID”字段为空。

  ![服务器信息](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. 单击“身份验证设置”，选择“证书”。 

  ![身份验证设置](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. 单击“选择…” 选择要用于身份验证的证书。

  ![证书](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. “选择标识”会显示可供选择的证书列表。 选择适当的证书，单击“继续”。

  ![identity](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. 在“本地 ID”字段中，指定证书的名称（见步骤 5）。 在本示例中，该名称为“ikev2Client.com”。 然后单击“应用”按钮保存所做的更改。

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. 在“网络”对话框中，单击“应用”保存所有更改。 然后单击“连接”，启动与 Azure VNet 的 P2S 连接。

## <a name="otherauth"></a>使用其他身份验证类型或协议

若要使用其他身份验证类型（例如 OTP）而不是用户名/密码或证书，或者要使用其他身份验证协议（例如，使用 PEAP-MSCHAPv2 而不是 EAP-MSCHAPv2），则必须创建自己的 VPN 客户端配置文件。 创建配置文件需要虚拟网关 IP 地址、隧道类型、拆分隧道路由等信息。 可通过以下步骤获取该信息：

1. 使用“Get-AzureRmVpnClientConfiguration”cmdlet 生成适用于 EapMSChapv2 的 VPN 客户端配置。 有关说明，请参阅相关文章的[此部分](#ccradius)。

2. 解压缩 VpnClientConfiguration.zip 文件，查找 GenenericDevice 文件夹。 忽略包含适用于 64 位和 32 位体系结构的 Windows 安装程序的文件夹。
 
3. GenenericDevice 文件夹包含名为 VpnSettings 的 XML 文件。 此文件包含所有必需的信息。

  * VpnServer - Azure VPN 网关的 FQDN。 这是客户端连接到的地址。
  * VpnType - 用来进行连接的隧道类型。
  * 路由 - 需要在配置文件中配置的路由，目的是通过 P2S 隧道发送流量时，仅发送 Azure VNet 绑定流量。
  * GenenericDevice 文件夹还包含一个名为“VpnServerRoot”的 .cer 文件。 该文件包含在 P2S 连接设置过程中验证 Azure VPN 网关所需的根证书。 在要连接到 Azure VNet 的所有设备上安装该证书。 
 
## <a name="next-steps"></a>后续步骤

返回到相关文章，[完成 P2S 配置](point-to-site-how-to-radius-ps.md)。
