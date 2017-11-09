---
title: "创建并安装用于 Azure 证书身份验证的 P2S VPN 客户端配置文件：PowerShell：Azure | Microsoft Docs"
description: "本文介绍如何为使用证书身份验证的点到站点连接创建并安装 VPN 客户端配置文件。"
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
ms.date: 09/27/2017
ms.author: cherylmc
ms.openlocfilehash: 7fe8d5e473e2c8281b1d6c8d7d5423294c428678
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>为本机 Azure 证书身份验证 P2S 配置创建并安装 VPN 客户端配置文件

VPN 客户端配置文件包含在一个 zip 文件中。 配置文件提供本机 Windows 或 Mac IKEv2 VPN 客户端通过使用本机 Azure 证书身份验证的点到站点连接，来与 VNet 建立连接所需的设置。

>[!NOTE]
>IKEv2 for P2S 目前以预览版提供。
>

### <a name="workflow"></a>P2S 工作流

  1. 为 P2S 连接设置 Azure VPN 网关。
  2. 生成根证书和客户端证书。 将根证书公钥上传到 Azure，并在客户端设备上安装客户端证书。 证书用于对连接方用户进行身份验证。
  3. 获取所选身份验证选项的 VPN 客户端配置，并使用该配置在 Windows 和 Mac 设备上设置 VPN 客户端。 这样，便可通过点到站点连接来与 Azure VNet 建立连接。

>[!NOTE]
>客户端配置文件特定于 VNet 的 VPN 配置。 如果在生成 VPN 客户端配置文件后，点到站点 VPN 配置（例如 VPN 协议类型或身份验证类型）发生变化，请务必为用户设备生成新的 VPN 客户端配置文件。
>
>

## <a name="generate"></a>生成 VPN 客户端配置文件

在开始之前，请确保所有连接方用户的设备上安装了有效的证书。 有关安装客户端证书的详细信息，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

可使用 PowerShell 或使用 Azure 门户生成客户端配置文件。 两种方法之一都会返回相同的 zip 文件。 解压缩该文件，查看以下文件夹：

  * **WindowsAmd64** 和 **WindowsX86**：分别包含 Windows 32 位和 64 位安装程序包。 **WindowsAmd64** 安装程序包适用于所有受支持的 64 位 Windows 客户端，而不仅仅是 Amd。
  * **Generic**：包含用于创建自己的 VPN 客户端配置的常规信息。 请忽略此文件夹。 如果网关上配置了 IKEv2 或 SSTP+IKEv2，会提供 Generic 文件夹。 如果仅配置了 SSTP，则不会提供 Generic 文件夹。

### <a name="zipportal"></a>使用 Azure 门户生成文件

1. 在 Azure 门户中，导航到要连接到的虚拟网络的虚拟网络网关。
2. 在虚拟网络网关页面上，单击“点到点配置”。
3. 在“点到站点配置”页的顶部，单击“下载 VPN 客户端”。 需要几分钟才能生成客户端配置包。
4. 浏览器会指示客户端配置 zip 文件可用。 其名称与网关名称相同。 解压缩该文件，查看文件夹。

### <a name="zipps"></a>使用 PowerShell 生成文件

1. 生成 VPN 客户端配置文件时，“-AuthenticationMethod”的值为“EapTls”。 使用以下命令生成 VPN 客户端配置文件：

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. 将 URL 复制到浏览器，下载 zip 文件，然后解压缩该文件，查看其中的文件夹。

## <a name="installwin"></a>安装 Windows VPN 客户端配置包

只要版本与 Windows 客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统列表，请参阅 [VPN 网关常见问题解答](vpn-gateway-vpn-faq.md#P2S)中的“点到站点”部分。

请使用以下步骤配置用于证书身份验证的本机 Windows VPN 客户端：

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。 
2. 双击所需的包进行安装。 如果显示 SmartScreen 弹出窗口，请单击“更多信息”，并单击“仍要运行”。
3. 在客户端计算机上，导航到“网络设置”，并单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。 

## <a name="installmac"></a>Mac 上的 VPN 客户端配置 (OSX)

Azure 不提供用于本机 Azure 证书身份验证的 mobileconfig 文件。 必须在将连接到 Azure 的每个 Mac 上手动配置本机 IKEv2 VPN 客户端。 Generic 文件夹包含配置该客户端所需的全部信息。 如果在下载中没有看到 Generic 文件夹，则可能 IKEv2 未选作隧道类型。 选择 IKEv2 后，再次生成 zip 文件，检索 Generic 文件夹。 Generic 文件夹包含以下文件：

* **VpnSettings.xml**：包含服务器地址和隧道类型等重要设置。 
* **VpnServerRoot.cer**：包含在 P2S 连接设置过程中验证 Azure VPN 网关所需的根证书。

使用以下步骤在 Mac 中配置用于证书身份验证的本机 VPN 客户端。 必须在将连接到 Azure 的每个 Mac 上完成以下步骤：

1. 将 **VpnServerRoot** 根证书导入 Mac。 为此，可将该文件复制到 Mac，并双击它。  
单击“添加”进行导入。

  ![添加证书](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >双击证书可能不会显示“添加”对话框，但该证书将安装在相应的存储中。 可以在证书类别下的登录密钥链中查找该证书。
  
2. 在“网络首选项”下打开“网络”对话框，单击“+”创建新的 VPN 客户端连接配置文件，以便通过 P2S 连接来与 Azure VNet 建立连接。

  “接口”值为“VPN”，“VPN 类型”值为“IKEv2”。 在“服务名称”字段中为配置文件指定一个名称，单击“创建”创建 VPN 客户端连接配置文件。

  ![网络](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. 从 **Generic** 文件夹中的 **VpnSettings.xml** 文件复制 **VpnServer** 标记值。 将该值粘贴到配置文件的“服务器地址”和“远程 ID”字段中。

  ![服务器信息](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. 单击“身份验证设置”，选择“证书”。 

  ![身份验证设置](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. 单击“选择…” 选择要用于身份验证的客户端证书。 客户端证书应已安装在计算机上（请参阅上面 **P2S 工作流**部分中的步骤 #2）。

  ![证书](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. “选择标识”会显示可供选择的证书列表。 选择适当的证书，单击“继续”。

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. 在“本地 ID”字段中，指定证书的名称（见步骤 6）。 在本示例中，该名称为“ikev2Client.com”。 然后单击“应用”按钮保存所做的更改。

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. 在“网络”对话框中，单击“应用”保存所有更改。 然后单击“连接”，启动与 Azure VNet 的 P2S 连接。

## <a name="next-steps"></a>后续步骤

返回到相关文章，[完成 P2S 配置](vpn-gateway-howto-point-to-site-rm-ps.md)。
