---
title: "为点到站点连接生成和导出证书：Makecert：Azure | Microsoft Docs"
description: "本文包含使用 MakeCert 创建自签名根证书、导出公钥和生成客户端证书的步骤。"
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
ms.date: 08/09/2017
ms.author: cherylmc
ms.openlocfilehash: 2beacc461370f268e54e1eedcb32939f7c606b14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>使用 Makecert 为点到站点连接生成并导出证书

点到站点连接使用证书进行身份验证。 本文说明如何使用 MakeCert 创建自签名根证书以及生成客户端证书。 如果正在寻找点到站点配置步骤（例如，如何上传根证书），请从下面的列表选择一篇关于“配置点到站点”的文章：

> [!div class="op_single_selector"]
> * [创建自签名证书 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [创建自签名证书 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [配置点到站点 - Resource Manager - Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [配置点到站点 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [配置点到站点 - 经典 - Azure 门户](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

虽然我们建议使用 [Windows 10 PowerShell 步骤](vpn-gateway-certificates-point-to-site.md)来创建证书，但提供这些 MakeCert 说明作为备选方法。 使用任一方法生成的证书均可安装在[支持的所有客户端操作系统](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)上。 然而，MakeCert 具有以下限制：

* 已弃用 Makecert。 这意味着此工具随时可能被删除。 MakeCert 不再可用时，使用 MakeCert 生成的所有证书均不会受到任何影响。 MakeCert 仅用于生成证书，不用作验证机制。

## <a name="rootcert"></a>创建自签名根证书

以下步骤演示如何使用 MakeCert 创建自签名证书。 这些步骤并非特定于部署模型。 它们同样适用于 Resource Manager 和经典部署模型。

1. 下载并安装 [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx)。
2. 安装后，通常可在此路径中找到 makecert.exe 实用工具：“C:\Program Files (x86)\Windows Kits\10\bin\<arch>”。 但它也有可能安装到了另一位置。 以管理员身份打开命令提示符，并导航到 MakeCert 实用工具所在位置。 可使用以下示例，调整到适当的位置：

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. 在计算机上的“个人”证书存储中创建并安装证书。 以下示例将创建一个相应的 *.cer* 文件，在配置 P2S 时需要将此文件上传到 Azure。 使用想要用于证书的名称替换“P2SRootCert”和“P2SRootCert.cer”。 该证书位于“Certificates - Current User\Personal\Certificates”中。

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>导出公钥 (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

必须将 exported.cer 文件上传到 Azure。 有关说明，请参阅[配置点到站点连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)。 若要添加其他受信任的根证书，请参阅文章的[此部分](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add)。

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>导出自签名证书和私钥以将其存储（可选）

可能想要导出自签名根证书并将它存储在安全位置。 如果需要，可以稍后在另一台计算机上安装此自签名证书，并生成更多客户端证书，或导出另一个 .cer 文件。 要将自签名根证书导出为 .pfx，请选择该根证书，然后使用[导出客户端证书](#clientexport)中所述的步骤导出。

## <a name="create-and-install-client-certificates"></a>创建和安装客户端证书

不要直接在客户端计算机上安装自签名的证书。 需要从自签名证书生成客户端证书。 然后导出客户端证书，并将其安装到客户端计算机上。 以下步骤并非特定于部署模型。 它们同样适用于 Resource Manager 和经典部署模型。

### <a name="clientcert"></a>生成客户端证书

在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 可以从自签名根证书生成客户端证书，导出并安装该客户端证书。 如果不安装客户端证书，身份验证会失败。 

以下步骤引导完成从自签名根证书生成客户端证书的过程。 可以从相同根证书生成多个客户端证书。 使用以下步骤生成客户端证书时，客户端证书会自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，可以导出该证书。
 
1. 在用于创建自签名证书的同一台计算机上，以管理员身份打开命令提示符。
2. 修改并运行示例，生成客户端证书。
  * 将“P2SRootCert”更改为生成客户端证书所用的自签名根证书的名称。 确保使用的是根证书的名称，即创建根证书时指定的“CN=”值。
  * 将 P2SChildCert 更改为生成客户端证书所用的名称。

  如果未经修改就运行以下示例，则个人证书存储中将有一个从根证书 P2SRootCert 生成的客户端证书，名为 P2SChildcert。

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>导出客户端证书

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>安装已导出的客户端证书

若要安装客户端证书，请参阅[安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="next-steps"></a>后续步骤

继续使用点到站点配置。 

* 有关**资源管理器**部署模型步骤，请参阅[使用本机 Azure 证书身份验证配置 P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
* 有关**经典**部署模型步骤，请参阅 [Configure a Point-to-Site VPN connection to a VNet (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)（配置与 VNet 的点到站点 VPN 连接（经典））。
