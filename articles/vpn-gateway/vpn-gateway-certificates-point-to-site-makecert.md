---
title: "为点到站点连接创建和导出证书：Makecert：Azure | Microsoft Docs"
description: "本文包含使用 Makecert 创建自签名根证书、导出公钥和生成客户端证书的步骤。"
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
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 0800a7754241eb409dbd86db82b586a3e19a29fc
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>使用 Makecert 为点到站点连接生成并导出证书

> [!NOTE]
> 仅当无权访问 Windows 10 计算机以为点到站点连接生成自签名证书时才使用这些说明。 将弃用 Makecert。 此外，Makecert 无法创建 SHA-2 证书，只能创建 SHA-1 证书（它对 P2S 仍然有效）。 出于这些原因，在可能的情况下，建议使用 [PowerShell 步骤](vpn-gateway-certificates-point-to-site.md)。 使用 PowerShell 或 Makecert 创建的证书可以安装在任何[支持的客户端操作系统](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)上，而不仅仅是用于创建这些证书的操作系统上。
>
>


本文将说明创建自签名根证书、导出公钥，以及生成客户端证书。 本文不包含点到站点配置说明或点到站点常见问题解答。 可以通过从以下列表中选择其中一篇“配置点到站点”文章来查找该信息：

> [!div class="op_single_selector"]
> * [创建自签名证书 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [创建自签名证书 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [配置点到站点 - Resource Manager - Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [配置点到站点 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [配置点到站点 - 经典 - Azure 门户](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

点到站点连接使用证书进行身份验证。 配置点到站点连接时，需要将根证书的公钥（.cer 文件）上载到 Azure。 此外，必须从根证书生成客户端证书，并将其安装在连接到 VNet 的每台客户端计算机上。 客户端证书允许客户端进行身份验证。

## <a name="rootcert"></a>创建自签名根证书

以下步骤演示如何使用 Makecert 创建自签名证书。 这些步骤并非特定于部署模型。 它们同样适用于 Resource Manager 和经典部署模型。

1. 下载并安装 [Makecert](https://msdn.microsoft.com/en-us/library/windows/desktop/aa386968(v=vs.85).aspx)。
2. 安装后，可以在此路径中找到 makecert.exe 实用工具：“C:\Program Files (x86)\Windows Kits\10\bin\<arch>”。 以管理员身份打开命令提示符，并导航到 makecert 实用工具所在位置。 可以使用以下示例：

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. 在计算机上的“个人”证书存储中创建并安装证书。 以下示例将创建一个相应的 *.cer* 文件，在配置 P2S 时需要将此文件上传到 Azure。 使用想要用于证书的名称替换“P2SRootCert”和“P2SRootCert.cer”。 该证书将位于“证书”-“当前用户\个人\证书”中。

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha1 -len 2048 -ss My "P2SRootCert.cer"
  ```

## <a name="cer"></a>导出公钥 (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

必须将 exported.cer 文件上传到 Azure。 有关说明，请参阅[配置点到站点连接](vpn-gateway-howto-point-to-site-rm-ps.md#upload)。

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>导出自签名证书和私钥以将其存储（可选）

你可能想要导出自签名根证书并将它存储在安全位置。 如果需要，可以稍后在另一台计算机上安装此自签名证书，然后生成更多客户端证书，或导出另一个 .cer 文件。 若要将自签名根证书导出为 .pfx，请选择该根证书，然后使用[导出客户端证书](#clientexport)中所述的步骤导出。

## <a name="create-and-install-client-certificates"></a>创建和安装客户端证书

不要直接在客户端计算机上安装自签名的证书。 您需要从自签名证书生成客户端证书。 然后导出客户端证书并安装到客户端计算机上。 以下步骤并非特定于部署模型。 它们同样适用于 Resource Manager 和经典部署模型。

### <a name="clientcert"></a>生成客户端证书

在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 可以从自签名根证书生成客户端证书，然后导出并安装该客户端证书。 如果不安装客户端证书，身份验证将会失败。 

以下步骤引导你完成从自签名根证书生成客户端证书的过程。 可以从相同根证书生成多个客户端证书。 使用以下步骤生成客户端证书时，客户端证书将自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，可以导出该证书。
 
1. 在用于创建自签名证书的同一台计算机上，以管理员身份打开命令提示符。
2. 修改并运行示例，生成客户端证书。
  * 将“P2SRootCert”更改为生成客户端证书所用的自签名根证书的名称。 确保使用的是根证书的名称，即创建根证书时指定的“CN=”值。
  * 将 P2SChildCert 更改为生成客户端证书所用的名称。

  如果未经修改就运行以下示例，则个人证书存储中将有一个从根证书 P2SRootCert 生成的客户端证书，名为 P2SChildcert。

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha1
  ```

### <a name="clientexport"></a>导出客户端证书

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>安装已导出的客户端证书

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>后续步骤

继续使用点到站点配置。 

* 有关 **Resource Manager** 部署模型步骤，请参阅 [Configure a Point-to-Site connection to a VNet](vpn-gateway-howto-point-to-site-resource-manager-portal.md)（配置与 VNet 的点到站点连接）。
* 有关**经典**部署模型步骤，请参阅 [Configure a Point-to-Site VPN connection to a VNet (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)（配置与 VNet 的点到站点 VPN 连接（经典））。
