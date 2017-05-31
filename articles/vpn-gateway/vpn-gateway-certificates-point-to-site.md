---
title: "为点到站点连接创建和导出证书：PowerShell：Azure | Microsoft Docs"
description: "本文包含使用 PowerShell 在 Windows 10 上创建自签名根证书、导出公钥和生成客户端证书的步骤。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 1bfcb8683669770d6dd927cbde53a683bbc1d56e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell"></a>使用 PowerShell 为点到站点连接生成并导出证书

本文说明了如何创建自签名根证书以及生成客户端证书。 本文不包含点到站点配置说明或点到站点常见问题解答。 可以通过从以下列表中选择其中一篇“配置点到站点”文章来查找该信息：

> [!div class="op_single_selector"]
> * [创建自签名证书 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [创建自签名证书 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [配置点到站点 - Resource Manager - Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [配置点到站点 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [配置点到站点 - 经典 - Azure 门户](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

点到站点连接使用证书进行身份验证。 配置点到站点连接时，需要将根证书的公钥（.cer 文件）上载到 Azure。 此外，必须从根证书生成客户端证书，并将其安装在连接到 VNet 的每台客户端计算机上。 客户端证书允许客户端进行身份验证。


> [!NOTE]
> 必须在运行 Windows 10 的计算机上执行本文中的步骤。 生成证书所需的 PowerShell cmdlet 是 Windows 10 操作系统的一部分，并且在其他版本的 Windows 上不起作用。 生成证书只需要这些 cmdlet。 生成证书后，可以在任何支持的客户端操作系统上安装它们。 如果无权访问 Windows 10 计算机，则可以使用 Makecert 生成证书。 但是，使用 Makecert 生成的证书虽然与点到站点连接兼容，但不是 SHA-2。 有关 Makecert 说明，请参阅[使用 Makecert 创建证书](vpn-gateway-certificates-point-to-site-makecert.md)。 使用 PowerShell 或 Makecert 创建的证书可以安装在任何[支持的客户端操作系统](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)上，而不仅仅是用于创建这些证书的操作系统上。
> 
>

## <a name="rootcert"></a>创建自签名根证书

以下步骤演示如何在 Windows 10 上使用 PowerShell 创建自签名根证书。 这些步骤中使用的 cmdlet 和参数是 Windows 10 操作系统的一部分，而不是 PowerShell 版本的一部分。 这并不意味着，所创建的证书只能在 Windows 10 上安装。 可以在任何支持的客户端上安装它们。 有关支持的客户端的信息，请参阅[点到站点常见问题解答](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)。

1. 在运行 Windows 10 的计算机上，使用提升的特权打开 Windows PowerShell 控制台。
2. 使用以下示例创建自签名根证书。 以下示例创建名为“P2SRootCert”、将自动安装在“Certificates-Current User\Personal\Certificates”中的自签名根证书。 打开“certmgr.msc”或“管理用户证书”，即可查看证书。

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>导出公钥 (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

必须将 exported.cer 文件上传到 Azure。 有关说明，请参阅[配置点到站点连接](vpn-gateway-howto-point-to-site-rm-ps.md#upload)。

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>导出自签名根证书和公钥以将其存储（可选）

你可能想要导出自签名根证书并将它存储在安全位置。 如果需要，可以稍后在另一台计算机上安装此自签名证书，然后生成更多客户端证书，或导出另一个 .cer 文件。 若要将自签名根证书导出为 .pfx，请选择该根证书，然后使用[导出客户端证书](#clientexport)中所述的步骤导出。

## <a name="clientcert"></a>生成客户端证书

在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 可以从自签名根证书生成客户端证书，然后导出并安装该客户端证书。 如果不安装客户端证书，身份验证将会失败。 

以下步骤引导你完成从自签名根证书生成客户端证书的过程。 可以从相同根证书生成多个客户端证书。 使用以下步骤生成客户端证书时，客户端证书将自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，可以导出该证书。

使用以下 PowerShell 步骤生成客户端证书时，需要 Windows 10。 这些步骤中使用的 cmdlet 和参数是 Windows 10 操作系统的一部分，而不是 PowerShell 版本的一部分。 这并不意味着，所创建的证书只能在 Windows 10 上安装。 有关支持的客户端的信息，请参阅[点到站点常见问题解答](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)。

### <a name="example-1"></a>示例 1

此示例使用上一部分中声明的“$cert”变量。 如果创建自签名根证书后关闭了 PowerShell 控制台，或者要在新的 PowerShell 控制台会话中创建其他客户端证书，请使用[示例 2](#ex2) 中的步骤。

修改并运行示例以生成客户端证书。 如果在未经修改的情况下直接运行以下示例，将会生成名为“P2SChildCert”的客户端证书。  如果想要为子证书指定其他名称，请修改 CN 值。 运行此示例时，请不要更改 TextExtension。 生成的客户端证书将自动安装在计算机上的“Certificates - Current User\Personal\Certificates”中。

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>示例 2

若要创建其他客户端证书，或者不想要使用创建自签名根证书时所用的同一个 PowerShell 会话，请使用以下步骤：

1. 识别安装在计算机上的自签名根证书。 此 cmdlet 将返回计算机上安装的证书列表。

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. 从返回的列表中找到使用者名称，然后将该名称旁边的指纹复制到文本文件中。 以下示例显示了两个证书。 CN 名称是要从中生成子证书的自签名根证书的名称。 在本例中，该根证书为“P2SRootCert”。

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. 使用上一步骤中获取的指纹为根证书声明变量。 将 THUMBPRINT 替换为要从中生成子证书的根证书的指纹。

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  例如，使用上一步骤中 P2SRootCert 的指纹，该变量将如下所示：

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```    

4.  修改并运行示例以生成客户端证书。 如果在未经修改的情况下直接运行以下示例，将会生成名为“P2SChildCert”的客户端证书。 如果想要为子证书指定其他名称，请修改 CN 值。 运行此示例时，请不要更改 TextExtension。 生成的客户端证书将自动安装在计算机上的“Certificates - Current User\Personal\Certificates”中。

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>导出客户端证书   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]
    

## <a name="install"></a>安装已导出的客户端证书

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>后续步骤

继续使用点到站点配置。 

* 有关 **Resource Manager** 部署模型步骤，请参阅 [Configure a Point-to-Site connection to a VNet](vpn-gateway-howto-point-to-site-resource-manager-portal.md)（配置与 VNet 的点到站点连接）。 
* 有关**经典**部署模型步骤，请参阅 [Configure a Point-to-Site VPN connection to a VNet (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)（配置与 VNet 的点到站点 VPN 连接（经典））。
