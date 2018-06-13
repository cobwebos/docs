---
title: 为点到站点连接生成和导出证书：PowerShell：Azure | Microsoft Docs
description: 在 Windows 10 或 Windows Server 2016 上使用 PowerShell 创建自签名根证书、导出公钥和生成客户端证书。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: cherylmc
ms.openlocfilehash: 385b6ed2e8104fd2e15e6e55d46dcd12b963ec6b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423042"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>使用 PowerShell 为点到站点连接生成和导出证书

点到站点连接使用证书进行身份验证。 本文介绍如何在 Windows 10 或 Windows Server 2016 上使用 PowerShell 创建自签名根证书并生成客户端证书。 如果正在寻找点到站点配置步骤（例如，如何上传根证书），请从下面的列表选择一篇关于“配置点到站点”的文章：

> [!div class="op_single_selector"]
> * [创建自签名证书 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [创建自签名证书 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [配置点到站点 - Resource Manager - Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [配置点到站点 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [配置点到站点 - 经典 - Azure 门户](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

必须在运行 Windows 10 或 Windows Server 2016 的计算机上执行本文中的步骤。 用于生成证书的 PowerShell cmdlet 是操作系统的一部分，在其他版本的 Windows 上不正常工作。 只需 Windows 10 或 Windows Server 2016 计算机即可生成证书。 生成证书后，可上传证书，或在任何支持的客户端操作系统上安装该证书。 

如果无法访问 Windows 10 或 Windows Server 2016 计算机，则可使用 [Makecert](vpn-gateway-certificates-point-to-site-makecert.md) 生成证书。 使用任一方法生成的证书均可安装在[支持的](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)所有客户端操作系统上。

## <a name="rootcert"></a>1.创建自签名根证书

使用 New-SelfSignedCertificate cmdlet 创建自签名根证书。 有关参数的其他信息，请参阅 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)。

1. 在运行 Windows 10 或 Windows Server 2016 的计算机上，使用提升的权限打开 Windows PowerShell 控制台。
2. 使用以下示例创建自签名根证书。 以下示例创建名为“P2SRootCert”、会自动安装在“Certificates-Current User\Personal\Certificates”中的自签名根证书。 打开“certmgr.msc”或“管理用户证书”，即可查看证书。

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

## <a name="clientcert"></a>2.生成客户端证书

在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 可以从自签名根证书生成客户端证书，导出并安装该客户端证书。 如果不安装客户端证书，身份验证会失败。 

以下步骤引导完成从自签名根证书生成客户端证书的过程。 可以从相同根证书生成多个客户端证书。 使用以下步骤生成客户端证书时，客户端证书会自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，可以导出该证书。

这些示例使用 New-SelfSignedCertificate cmdlet 生成有效期为一年的客户端证书。 有关参数的其他信息（例如为客户端证书设置其他有效期），请参阅 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)。

### <a name="example-1"></a>示例 1

此示例使用上一部分中声明的“$cert”变量。 如果创建自签名根证书后关闭了 PowerShell 控制台，或者要在新的 PowerShell 控制台会话中创建其他客户端证书，请使用[示例 2](#ex2) 中的步骤。

修改并运行示例以生成客户端证书。 如果在未经修改的情况下直接运行以下示例，会生成名为“P2SChildCert”的客户端证书。  如果想要为子证书指定其他名称，请修改 CN 值。 运行此示例时，请不要更改 TextExtension。 生成的客户端证书会自动安装在计算机上的“Certificates - Current User\Personal\Certificates”中。

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
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
2. 从返回的列表中找到使用者名称，并将该名称旁边的指纹复制到文本文件中。 以下示例显示了两个证书。 CN 名称是要从中生成子证书的自签名根证书的名称。 在本例中，该根证书为“P2SRootCert”。

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. 使用上一步骤中获取的指纹为根证书声明变量。 将 THUMBPRINT 替换为要从中生成子证书的根证书的指纹。

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  例如，使用上一步骤中 P2SRootCert 的指纹，该变量将如下所示：

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  修改并运行示例以生成客户端证书。 如果在未经修改的情况下直接运行以下示例，会生成名为“P2SChildCert”的客户端证书。 如果想要为子证书指定其他名称，请修改 CN 值。 运行此示例时，请不要更改 TextExtension。 生成的客户端证书会自动安装在计算机上的“Certificates - Current User\Personal\Certificates”中。

  ```powershell
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="cer"></a>3.导出根证书公钥 (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>导出自签名根证书和私钥以将其存储（可选）

可能需要导出自签名根证书并将它存储在安全位置作为备份。 如果需要，可以稍后在另一台计算机上安装此自签名根证书，并生成更多客户端证书。 要将自签名根证书导出为 .pfx，请选择该根证书，然后使用[导出客户端证书](#clientexport)中所述的步骤导出。

## <a name="clientexport"></a>4.导出客户端证书

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5.安装已导出的客户端证书

通过 P2S 连接连接到 VNet 的每个客户端都需要在本地安装客户端证书。

若要安装客户端证书，请参阅[为点到站点连接安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="install"></a>6.继续执行 P2S 配置步骤

继续使用点到站点配置。

* 有关**资源管理器**部署模型步骤，请参阅[使用本机 Azure 证书身份验证配置 P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。 
* 有关**经典**部署模型步骤，请参阅 [Configure a Point-to-Site VPN connection to a VNet (classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)（配置与 VNet 的点到站点 VPN 连接（经典））。
* 有关 P2S 故障排除信息，请参阅[排查 Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
