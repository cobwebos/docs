---
title: "创建并安装适用于 P2S RADIUS 连接的 VPN 客户端配置文件：PowerShell：Azure | Microsoft Docs"
description: "创建 Windows、Mac OS X 和 Linux VPN 客户端配置文件，以便使用 RADIUS 身份验证建立连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 1d57537428f5ac1085b6cbae93be6f77c71b12e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>创建并安装适用于 P2S RADIUS 身份验证的 VPN 客户端配置文件

若要通过点到站点 (P2S) 连接到虚拟网络，需要配置将从中进行连接的客户端设备。 可以通过 Windows、Mac OS X 和 Linux 客户端设备创建 P2S VPN 连接。 

使用 RADIUS 身份验证时，可以选择多种身份验证选项：用户名/密码身份验证、证书身份验证，以及其他身份验证类型。 每种身份验证的 VPN 客户端配置各个不同。 若要配置 VPN 客户端，可以使用包含所需设置的客户端配置文件。 本文将会帮助你创建并安装所要使用的 RADIUS 身份验证类型的 VPN 客户端配置。

P2S RADIUS 身份验证的配置工作流如下：

1. [设置适用于 P2S 连接的 Azure VPN 网关](point-to-site-how-to-radius-ps.md)。
2. [设置适用于身份验证的 RADIUS 服务器](point-to-site-how-to-radius-ps.md#radius)。 
3. **获取适用于所选身份验证选项的 VPN 客户端配置，用其设置 VPN 客户端**（本文）。
4. [完成 P2S 配置和连接](point-to-site-how-to-radius-ps.md)。

>[!IMPORTANT]
>如果在生成 VPN 客户端配置文件后，点到站点 VPN 配置（例如 VPN 协议类型或身份验证类型）发生了变化，则必须生成新的 VPN 客户端配置并将其安装在用户设备上。
>
>

若要执行本文中的操作，请先决定想要使用的身份验证类型：用户名/密码、证书或其他类型的身份验证。 每个部分都提供了适用于 Windows、Mac OS X 和 Linux 的步骤（目前可用的有限步骤）。

## <a name="adeap"></a>用户名/密码身份验证

可以将用户名/密码身份验证配置为使用 Active Directory，也可以将其配置为不使用 Active Directory。 使用每种方案时，请确保所有进行连接的用户的用户名/密码凭据可以通过 RADIUS 进行身份验证。

配置用户名/密码身份验证时，只能针对 EAP-MSCHAPv2 用户名/密码身份验证协议创建配置。 在命令中，`-AuthenticationMethod` 是 `EapMSChapv2`。

### <a name="usernamefiles"></a> 1.生成 VPN 客户端配置文件

生成 VPN 客户端配置文件来用于用户名/密码身份验证。 可以使用以下命令生成 VPN 客户端配置文件：

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
运行该命令将返回一个链接。 请将该链接复制并粘贴到 Web 浏览器，下载 **VpnClientConfiguration.zip**。 解压缩该文件，查看以下文件夹： 
 
* **WindowsAmd64** 和 **WindowsX86**：这些文件夹分别包含 Windows 64 位和 32 位安装程序包。 
* **Generic**：此文件夹包含你可以用来创建自己的 VPN 客户端配置的常规信息。 对于用户名/密码身份验证配置，不需要此文件夹。
* **Mac**：如果在创建虚拟网络网关时已配置了 IKEv2，则会看到名为 **Mac** 的文件夹，其中包含 **mobileconfig** 文件。 可以使用该文件配置 Mac 客户端。

如果已创建了客户端配置文件，可以使用 `Get-AzureRmVpnClientConfiguration` 检索这些文件。 但是，如果对 P2S VPN 配置（例如 VPN 协议类型或身份验证类型）进行更改，该配置不会自动更新。 必须运行 `New-AzureRmVpnClientConfiguration` cmdlet 才能创建新的配置下载。

若要检索以前生成的客户端配置文件，请使用以下命令：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2.配置 VPN 客户端

可配置以下 VPN 客户端：

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [使用 strongSwan 的 Linux](#adlinuxcli)
 
#### <a name="adwincli"></a>设置 Windows VPN 客户端

只要版本与 Windows 客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统的列表，请参阅[常见问题解答](vpn-gateway-vpn-faq.md#P2S)。

请使用以下步骤配置用于证书身份验证的本机 Windows VPN 客户端：

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择 **VpnClientSetupAmd64** 安装程序包。 对于 32 位处理器体系结构，请选择 **VpnClientSetupX86** 安装程序包。 
2. 若要安装该包，请双击它。 如果看到了 SmartScreen 弹出窗口，请选择“更多信息” > “仍要运行”。
3. 在客户端计算机上，浏览到“网络设置”，并选择“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。 

#### <a name="admaccli"></a>Mac (OS X) VPN 客户端设置

1. 选择 VpnClientSetup.mobileconfig 文件，将其发送给每个用户。 可以使用电子邮件或其他方法。

2. 在 Mac 中查找 mobileconfig 文件。

   ![Mobilconfig 文件的位置](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. 双击该配置文件以进行安装，然后选择“继续”。 配置文件名称与虚拟机的名称相同。

   ![安装消息](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. 选择“继续”来信任配置文件的发送者并继续进行安装。

   ![确认消息](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. 在配置文件安装过程中，可以指定用于 VPN 身份验证的用户名和密码。 此信息不是必须输入的。 如果进行指定，此信息在指定后会进行保存，在启动连接时自动使用。 选择“安装”以继续。

   ![用于 VPN 的用户名和密码](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. 输入用户名和密码，以便获取在计算机上安装该配置文件所需的权限。 选择“确定”。

   ![用于配置文件安装的用户名和密码框](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. 安装配置文件后，可在“配置文件”对话框中看到它。 以后还可以从“系统首选项”打开该对话框。

   ![“配置文件”对话框](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. 若要访问 VPN 连接，请从“系统首选项”打开“网络”对话框。

   ![“系统首选项”中的图标](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN 连接显示为“IkeV2-VPN”。 可以通过更新 **mobileconfig** 文件来更改该名称。

   ![VPN 连接的详细信息](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. 选择“身份验证设置”。 在列表中选择“用户名”，并输入凭据。 如果此前已输入凭据，系统会在列表中自动选择“用户名”，并预填充用户名和密码。 选择“确定”以保存设置。

    ![身份验证设置](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. 返回到“网络”对话框，选择“应用”以保存更改。 若要启动连接，请选择“连接”。

#### <a name="adlinuxcli"></a>通过 strongSwan 设置 Linux VPN 客户端

以下说明是通过 Ubuntu 17.0.4 上的 strongSwan 5.5.1 创建的。 根据 Linux 和 strongSwan 的版本，实际屏幕可能有所差异。

1. 打开**终端**并运行示例中的命令，安装 **strongSwan** 及其网络管理器。 如果收到与 `libcharon-extra-plugins` 相关的错误，请将其替换为 `strongswan-plugin-eap-mschapv2`。

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. 选择“网络管理器”图标（向上/向下箭头），然后选择“编辑连接”。

   ![网络管理器中的“编辑连接”选择项](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. 选择“添加”按钮以创建新连接。

   ![连接的“添加”按钮](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. 从下拉菜单中选择“IPsec/IKEv2 (strongswan)”，然后选择“创建”。 可以在此步骤中重命名连接。

   ![选择连接类型](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. 在客户端配置文件下载到的 **Generic** 文件夹中打开 **VpnSettings.xml** 文件。 查找名为 `VpnServer` 的标记并复制该名称，以 `azuregateway` 开头以 `.cloudapp.net` 结尾。

   ![VpnSettings.xml 文件的内容](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. 在“网关”部分中，将此名称粘贴到新 VPN 连接的“地址”字段中。 接下来，选择“证书”字段末尾的文件夹图标，浏览到 **Generic** 文件夹，并选择 **VpnServerRoot** 文件。
7. 在连接的“客户端”部分中，选择“EAP”作为“身份验证”方式，并输入用户名和密码。 可能需要选择右侧的锁形图标才能保存此信息。 然后选择“保存”。

   ![编辑连接设置](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. 选择“网络管理器”图标（向上/向下箭头），并将鼠标指针悬停在“VPN 连接”上。 将会看到已创建的 VPN 连接。 若要启动该连接，请选择它。

   ![网络管理器中的“VPN Radius”连接](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>证书身份验证
 
可以创建适用于 RADIUS 证书身份验证的 VPN 客户端配置文件，该身份验证使用 EAP-TLS 协议。 通常情况下，企业颁发的证书用于对 VPN 用户进行身份验证。 请确保所有进行连接的用户均已在其设备上安装了证书，并且你的 RADIUS 服务器可以验证该证书。

在命令中，`-AuthenticationMethod` 是 `EapTls`。 在证书身份验证过程中，客户端通过验证 RADIUS 服务器的证书完成该服务器的验证。 `-RadiusRootCert` 是包含用来验证 RADIUS 服务器的根证书的 .cer 文件。

需在每个 VPN 客户端设备上安装客户端证书。 有时候，一个 Windows 设备有多个客户端证书。 在身份验证过程中，这会导致系统弹出一个对话框，其中列出所有证书。 然后，用户必须选择要使用的证书。 可以通过指定客户端证书应链接到的根证书，筛选出正确的证书。 

`-ClientRootCert` 是包含该根证书的 .cer 文件。 它是一个可选参数。 如果要从中进行连接的设备只有一个客户端证书，则不需要指定此参数。

### <a name="certfiles"></a>1.生成 VPN 客户端配置文件

生成 VPN 客户端配置文件来用于证书身份验证。 可以使用以下命令生成 VPN 客户端配置文件：
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

运行该命令将返回一个链接。 将该链接复制并粘贴到 Web 浏览器，下载 VpnClientConfiguration.zip。 解压缩该文件，查看以下文件夹：

* **WindowsAmd64** 和 **WindowsX86**：这些文件夹分别包含 Windows 64 位和 32 位安装程序包。 
* **GenericDevice**：此文件夹包含用于创建你自己的 VPN 客户端配置的常规信息。

如果已创建了客户端配置文件，可以使用 `Get-AzureRmVpnClientConfiguration` 检索这些文件。 但是，如果对 P2S VPN 配置（例如 VPN 协议类型或身份验证类型）进行更改，该配置不会自动更新。 必须运行 `New-AzureRmVpnClientConfiguration` cmdlet 才能创建新的配置下载。

若要检索以前生成的客户端配置文件，请使用以下命令：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2.配置 VPN 客户端

可配置以下 VPN 客户端：

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux（支持，但尚未编写步骤）

#### <a name="certwincli"></a>设置 Windows VPN 客户端

1. 选择一个配置包，将其安装在客户端设备上。 对于 64 位处理器体系结构，请选择 **VpnClientSetupAmd64** 安装程序包。 对于 32 位处理器体系结构，请选择 **VpnClientSetupX86** 安装程序包。 如果看到了 SmartScreen 弹出窗口，请选择“更多信息” > “仍要运行”。 也可将要安装的包保存在其他客户端计算机上。
2. 每个客户端都需要使用客户端证书来进行身份验证。 安装客户端证书。 有关客户端证书的信息，请参阅[用于点到站点连接的客户端证书](vpn-gateway-certificates-point-to-site.md)。 若要安装生成的证书，请参阅[在 Windows 客户端上安装证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。
3. 在客户端计算机上，浏览到“网络设置”，并选择“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。

#### <a name="certmaccli"></a>Mac (OS X) VPN 客户端设置

必须为连接到 Azure 虚拟网络的每个 Mac 设备创建一个单独的配置文件。 因为这些设备要求在配置文件中指定用于身份验证的用户证书。 **Generic** 文件夹包含创建配置文件所需的全部信息：

* VpnSettings.xml 包含服务器地址和隧道类型等重要设置。
* **VpnServerRoot.cer** 包含在设置 P2S 连接期间验证 VPN 网关所需的根证书。
* **RadiusServerRoot.cer** 包含在身份验证过程中验证 RADIUS 服务器所需的根证书。

使用以下步骤在 Mac 中配置用于证书身份验证的本机 VPN 客户端：

1. 将 **VpnServerRoot** 和 **RadiusServerRoot** 根证书导入到 Mac 中。 将每个文件复制到你的 Mac，双击它，然后选择“添加”。

   ![添加 VpnServerRoot 证书](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![添加 RadiusServerRoot 证书](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. 每个客户端都需要使用客户端证书来进行身份验证。 在客户端设备上安装客户端证书。
3. 在“网络首选项”下打开“网络”对话框。 选择 **+** 来为到 Azure 虚拟网络的 P2S 连接创建新的 VPN 客户端连接配置文件。

   “接口”值为“VPN”，“VPN 类型”值为“IKEv2”。 在“服务名称”框中为配置文件指定一个名称，然后选择“创建”以创建 VPN 客户端连接配置文件。

   ![接口和服务名称信息](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. 从 **Generic** 文件夹中的 **VpnSettings.xml** 文件复制 **VpnServer** 标记值。 将该值粘贴到配置文件的“服务器地址”和“远程 ID”框中。 将“本地 ID”框保留为空。

   ![服务器信息](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. 选择“身份验证设置”，然后选择“证书”。 

   ![身份验证设置](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. 单击“选择”以选择要用于身份验证的证书。

   ![选择用于身份验证的证书](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. “选择标识”会显示可供选择的证书列表。 选择适当的证书，然后选择“继续”。

   ![“选择标识”列表](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. 在“本地 ID”框中，指定证书的名称（见步骤 6）。 在本例中，该名称为 **ikev2Client.com**。然后，选择“应用”按钮以保存更改。

   ![“本地 ID”框](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. 在“网络”对话框中，选择“应用”以保存所有更改。 然后，选择“连接”以启动到 Azure 虚拟网络的 P2S 连接。

## <a name="otherauth"></a>使用其他身份验证类型或协议

若要使用其他身份验证类型（例如 OTP），或者要使用其他身份验证协议（例如，使用 PEAP-MSCHAPv2 而不是 EAP-MSCHAPv2），则必须创建自己的 VPN 客户端配置文件。 创建配置文件需要虚拟网关 IP 地址、隧道类型、拆分隧道路由等信息。 可通过以下步骤获取该信息：

1. 使用 `Get-AzureRmVpnClientConfiguration` cmdlet 生成适用于 EapMSChapv2 的 VPN 客户端配置。 有关说明，请参阅相关文章的[此部分](#ccradius)。

2. 解压缩 VpnClientConfiguration.zip 文件，查找 **GenenericDevice** 文件夹。 忽略包含适用于 64 位和 32 位体系结构的 Windows 安装程序的文件夹。
 
3. **GenenericDevice** 文件夹包含名为 **VpnSettings** 的 XML 文件。 此文件包含所有必需的信息：

   * **VpnServer**：Azure VPN 网关的 FQDN。 这是客户端连接到的地址。
   * **VpnType**：用来进行连接的隧道类型。
   * **路由**：为了仅通过 P2S 隧道发送为 Azure 虚拟机绑定的流量而需要在配置文件中配置的路由。
   
   **GenenericDevice** 文件夹还包含一个名为 **VpnServerRoot** 的 .cer 文件。 该文件包含在设置 P2S 连接期间验证 Azure VPN 网关所需的根证书。 在要连接到 Azure 虚拟网络的所有设备上安装该证书。

## <a name="next-steps"></a>后续步骤

返回到相关文章，[完成 P2S 配置](point-to-site-how-to-radius-ps.md)。

有关 P2S 故障排除信息，请参阅[排查 Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。