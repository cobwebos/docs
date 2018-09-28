---
title: 如何为 Azure VPN 网关配置 OpenVPN 客户端 |Microsoft Docs
description: 为 Azure VPN 网关配置 OpenVPN 客户端的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977836"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>为 Azure VPN 网关配置 OpenVPN 客户端（预览）

本文有助于配置 OpenVPN 客户端。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应该用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

验证你已完成为 VPN 网关配置 OpenVPN 的步骤。 有关详细信息，请参阅[为 Azure VPN 网关配置 OpenVPN](vpn-gateway-howto-openvpn.md)。

## <a name="windows"></a>Windows 客户端

1. 从官方 [OpenVPN 网站](https://openvpn.net/index.php/open-source/downloads.html)下载并安装 OpenVPN 客户端。
2. 下载网关的 VPN 配置文件。 可通过 Azure 门户中的“点对点”配置选项卡或 PowerShell 中的 New-AzureRmVpnClientConfiguration 来完成此操作。
3. 解压缩该配置文件。 然后，从记事本中的 OpenVPN 文件夹中打开 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，你只需打开 .cer 文件并通过证书标头之间的 base64 密钥进行复制。 请参阅此处，了解如何导出证书以获取已编码的公钥。
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的详细信息，请参阅[导出密钥](vpn-gateway-certificates-point-to-site.md#clientexport)。
6. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
7. 将 vpnconfig.ovpn 文件复制到 C:\Program Files\OpenVPN\config 文件夹。
8. 右键单击系统托盘中的 OpenVPN 图标，然后单击“连接”。

## <a name="mac"></a>Mac 客户端

1. 下载并安装 OpenVPN 客户端，如 [TunnelBlik](https://tunnelblick.net/downloads.html)。 
2. 下载网关的 VPN 配置文件。 这可通过 Azure 门户中的“点对点”配置选项卡来完成，也可以使用 PowerShell 中的 New-AzureRmVpnClientConfiguration 来完成。
3. 解压缩该配置文件。 从记事本中的 OpenVPN 文件夹中打开 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，你只需打开 .cer 文件并通过证书标头之间的 base64 密钥进行复制。 有关如何导出证书以获取已编码的公钥的详细信息，请参阅[导出公钥](vpn-gateway-certificates-point-to-site.md#cer)
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的详细信息，请参阅[导出私钥](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/)。
6. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
7. 双击配置文件以在 tunnelblick 中创建配置文件。
8. 启动应用程序文件夹中的 Tunnelblik。
9. 单击系统托盘中的 Tunneblik 图标，然后单击“连接”。

## <a name="linux"></a>Linux 客户端

1. 打开新的终端会话。 可以同时按“Ctrl + Alt + T”打开新会话
2. 输入以下命令以安装所需的组件：

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. 下载网关的 VPN 配置文件。 这可通过 Azure 门户中的“点对点”配置选项卡来完成，也可以使用 PowerShell 中的 New-AzureRmVpnClientConfiguration 来完成。
4. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关如何提取私钥的详细信息，请参阅[导出私钥](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/)。
5. 要使用命令行进行连接，请键入以下内容：
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. 要使用 GUI 进行连接，请转到系统设置。
6. 单击 + 添加新的 VPN 连接。
7. 在“添加 VPN”下，选择“从文件导入...”
8. 浏览到配置文件，然后双击或选择“打开”
9. 单击“添加 VPN”窗口上的“添加”。
  
  ![从文件导入](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. 可以通过在“网络设置”页面上或在系统托盘中的网络图标下打开 VPN 进行连接。

## <a name="next-steps"></a>后续步骤

如果你想要 VPN 客户端能够访问另一个 vnet（生产）中的资源，请按照 [Vnet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 文章中的说明设置 vnet-to-vnet 连接。 请确保在网关和连接上启用 BGP，否则流量不会流动。
