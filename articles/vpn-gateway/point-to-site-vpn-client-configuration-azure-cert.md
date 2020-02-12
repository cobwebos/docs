---
title: 创建 & 安装 P2S VPN 客户端配置文件：证书身份验证
titleSuffix: Azure VPN Gateway
description: 为 P2S 证书身份验证配置创建并安装 Windows、Linux、Linux (strongSwan) 和 Mac OS X VPN 客户端配置文件。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/15/2020
ms.author: cherylmc
ms.openlocfilehash: 18a9578cc454ea5259b9564d64dcd4308ee5ef87
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148961"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>为本机 Azure 证书身份验证 P2S 配置创建并安装 VPN 客户端配置文件

VPN 客户端配置文件包含在一个 zip 文件中。 配置文件提供本机 Windows、Mac IKEv2 VPN 或 Linux 客户端通过使用本机 Azure 证书身份验证的点到站点连接，来与 VNet 建立连接所需的设置。

客户端配置文件特定于 VNet 的 VPN 配置。 如果在生成 VPN 客户端配置文件后，点到站点 VPN 配置（例如 VPN 协议类型或身份验证类型）发生变化，请务必为用户设备生成新的 VPN 客户端配置文件。 

* 有关点到站点连接的详细信息，请参阅[关于点到站点 VPN](point-to-site-about.md)。
* 有关 OpenVPN 说明，请参阅[为 P2S 配置 OpenVPN](vpn-gateway-howto-openvpn.md) 和[配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md)。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>生成 VPN 客户端配置文件

在开始之前，请确保所有连接方用户的设备上安装了有效的证书。 有关安装客户端证书的详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

可使用 PowerShell 或使用 Azure 门户生成客户端配置文件。 两种方法之一都会返回相同的 zip 文件。 解压缩该文件，查看以下文件夹：

  * **WindowsAmd64** 和 **WindowsX86**：分别包含 Windows 32 位和 64 位安装程序包。 **WindowsAmd64** 安装程序包适用于所有受支持的 64 位 Windows 客户端，而不仅仅是 Amd。
  * **Generic**：包含用于创建自己的 VPN 客户端配置的常规信息。 如果网关上配置了 IKEv2 或 SSTP+IKEv2，会提供 Generic 文件夹。 如果仅配置了 SSTP，则不会提供 Generic 文件夹。

### <a name="zipportal"></a>使用 Azure 门户生成文件

1. 在 Azure 门户中，导航到要连接到的虚拟网络的虚拟网络网关。
2. 在虚拟网络网关页面上，单击“点到点配置”。
3. 在“点到站点配置”页的顶部，单击“下载 VPN 客户端”。 需要几分钟才能生成客户端配置包。
4. 浏览器会指示客户端配置 zip 文件可用。 其名称与网关名称相同。 解压缩该文件，查看文件夹。

### <a name="zipps"></a>使用 PowerShell 生成文件


1. 生成 VPN 客户端配置文件时，“-AuthenticationMethod”的值为“EapTls”。 使用以下命令生成 VPN 客户端配置文件：

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. 将 URL 复制到浏览器，下载 zip 文件，然后解压缩该文件，查看其中的文件夹。

## <a name="installwin"></a>Windows

只要版本与 Windows 客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统列表，请参阅 [VPN 网关常见问题解答](vpn-gateway-vpn-faq.md#P2S)中的“点到站点”部分。

>[!NOTE]
>在要从其进行连接的 Windows 客户端计算机上，必须拥有管理员权限。
>
>

请使用以下步骤配置用于证书身份验证的本机 Windows VPN 客户端：

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。 
2. 双击所需的包进行安装。 如果显示 SmartScreen 弹出窗口，请单击“更多信息”，并单击“仍要运行”。
3. 在客户端计算机上，导航到“网络设置”，并单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。 
4. 尝试连接前，请验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。 有关生成证书的详细信息，请参阅[生成证书](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="installmac"></a>Mac (OS X)

 必须在将连接到 Azure 的每个 Mac 上手动配置本机 IKEv2 VPN 客户端。 Azure 不提供用于本机 Azure 证书身份验证的 mobileconfig 文件。 **Generic** 包含需要用于配置的所有信息。 如果在下载中没有看到 Generic 文件夹，则可能 IKEv2 未选作隧道类型。 请注意，VPN 网关基本 SKU 不支持 IKEv2。 选择 IKEv2 后，再次生成 zip 文件，检索 Generic 文件夹。<br>Generic 文件夹包含以下文件：

* **VpnSettings.xml**：包含服务器地址和隧道类型等重要设置。 
* **VpnServerRoot.cer**：包含在 P2S 连接设置过程中验证 Azure VPN 网关所需的根证书。

使用以下步骤在 Mac 中配置用于证书身份验证的本机 VPN 客户端。 必须在将连接到 Azure 的每个 Mac 上完成以下步骤：

1. 将 **VpnServerRoot** 根证书导入 Mac。 为此，可将该文件复制到 Mac，并双击它。 单击“添加”进行导入。

   ![添加证书](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >双击证书可能不会显示“添加”对话框，但该证书将安装在相应的存储中。 可以在证书类别下的登录密钥链中查找该证书。
    >
  
2. 验证已安装由根证书颁发的客户端证书，该根证书在配置 P2S 设置时已上传到 Azure。 这不同于上一步中安装的 VPNServerRoot。 客户端证书可用于身份验证，且是必需的。 有关生成证书的详细信息，请参阅[生成证书](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。
3. 在“网络首选项”下打开“网络”对话框，单击“+”创建新的 VPN 客户端连接配置文件，以便通过 P2S 连接来与 Azure VNet 建立连接。

   “接口”值为“VPN”，“VPN 类型”值为“IKEv2”。 在“服务名称”字段中为配置文件指定一个名称，单击“创建”创建 VPN 客户端连接配置文件。

   ![网络](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. 从 **Generic** 文件夹中的 **VpnSettings.xml** 文件复制 **VpnServer** 标记值。 将该值粘贴到配置文件的“服务器地址”和“远程 ID”字段中。

   ![服务器信息](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. 单击“身份验证设置”，选择“证书”。 对于**Catalina**，单击 "**无**"，然后单击 "**证书**"

   ![身份验证设置](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * 对于 Catalina，选择 "**无**"，然后选择 "**证书**"。 **选择**正确的证书：
   
   ![catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. 单击“选择…” 选择要用于身份验证的客户端证书。 这是你在步骤 2 中安装的证书。

   ![证书 (certificate)](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. “选择标识”会显示可供选择的证书列表。 选择适当的证书，单击“继续”。

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. 在“本地 ID”字段中，指定证书的名称（见步骤 6）。 在本示例中，该名称为“ikev2Client.com”。 然后单击“应用”按钮保存所做的更改。

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. 在“网络”对话框中，单击“应用”保存所有更改。 然后单击“连接”，启动与 Azure VNet 的 P2S 连接。

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="installstrongswan"></a>安装 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>生成证书

如果尚未生成证书，请使用以下步骤：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>安装和配置

以下说明是在 Ubuntu 18.0.4 上创建的。 Ubuntu 16.0.10 不支持 strongSwan GUI。 如果想要使用 Ubuntu 16.0.10，则必须使用[命令行](#linuxinstallcli)。 以下示例可能与你看到的屏幕不同，具体取决于所用的 Linux 和 strongSwan 版本。

1. 打开**终端**并运行示例中的命令，安装 **strongSwan** 及其网络管理器。

   ```
   sudo apt install network-manager-strongswan
   ```
2. 选择 "**设置**"，然后选择 "**网络**"。

   ![编辑连接](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. 单击 " **+** " 按钮以创建新连接。

   ![添加连接](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. 从菜单中选择 " **IPsec/IKEv2 （strongSwan）** "，然后双击。 可在此步骤中命名连接。

   ![选择连接类型](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. 打开下载的客户端配置文件包含的 **Generic** 文件夹中的 **VpnSettings.xml** 文件。 找到名为 **VpnServer** 的标记，并复制以“azuregateway”开头、以“.cloudapp.net”结尾的名称。

   ![复制名称](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. 在“网关”部分中，将此名称粘贴到新 VPN 连接的“地址”字段中。 接下来，选择“证书”字段末尾的文件夹图标，浏览到 **Generic** 文件夹，并选择 **VpnServerRoot** 文件。
7. 在连接的“客户端”部分，为“身份验证”选择“证书/私钥”。 对于“证书”和“私钥”，请选择前面创建的证书和私钥。 在“选项”中，选择“请求内部 IP 地址”。 然后，单击“添加”。

   ![请求内部 IP 地址](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. **打开连接。**

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>安装 strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>生成证书

如果尚未生成证书，请使用以下步骤：

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>安装和配置

1. 从 Azure 门户下载 VPNClient 程序包。
2. 解压缩该文件。
3. 从 **Generic** 文件夹中，将 VpnServerRoot.cer 复制或移动 /etc/ipsec.d/cacerts。
4. 将 cp client.p12 复制或移动到 /etc/ipsec.d/private/。 此文件是 Azure VPN 网关的客户端证书。
5. 打开 VpnSettings.xml 文件并复制 `<VpnServer>` 值。 下一步骤中将使用此值。
6. 调整以下示例中的值，然后将该示例添加到 /etc/ipsec.conf 配置中。
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. 将以下内容添加到 */etc/ipsec.secrets*。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. 运行以下命令：

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>后续步骤

返回到相关文章，[完成 P2S 配置](vpn-gateway-howto-point-to-site-rm-ps.md)。

若要对 P2S 连接进行故障排除，请参阅以下文章：

  * [对 Azure 点到站点连接进行故障排除](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [对 Mac OS X VPN 客户端的 VPN 连接进行故障排除](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
