---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812646"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. 从官方网站下载并安装 OpenVPN 客户端。
1. 下载网关的 VPN 配置文件。 这可以通过 Azure 门户中的“用户 VPN 配置”选项卡或 PowerShell 中的 New-AzureRmVpnClientConfiguration 来完成。
1. 解压缩该配置文件。 从记事本中的 OpenVPN 文件夹中打开 vpnconfig.ovpn 配置文件。
1. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以打开 .cer 文件并在证书标头之间复制 base64 密钥。 有关步骤，请参阅[如何导出证书以获取已编码的公钥](../articles/virtual-wan/certificates-point-to-site.md)。
1. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关步骤，请参阅[如何提取私钥](../articles/virtual-wan/howto-openvpn-clients.md#windows)。
1. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
1. 将 vpnconfig.ovpn 文件复制到 C:\Program Files\OpenVPN\config 文件夹。
1. 右键单击系统托盘中的 OpenVPN 图标，然后选择“连接”。

##### <a name="ikev2"></a>IKEv2

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。
1. 双击所需的包进行安装。 如果看到弹出 SmartScreen，选择“详细信息”，然后选择“仍要运行” 。
1. 在客户端计算机上，导航到“网络设置”，并选择“VPN” 。 VPN 连接显示所连接到的虚拟网络的名称。
1. 尝试连接前，请验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。 有关生成证书的详细信息，请参阅[生成证书](../articles/virtual-wan/certificates-point-to-site.md)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)。
