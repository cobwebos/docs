---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/10/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 89fa06dda418f328b3bc07aada49aa347e35220a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182178"
---
## <a name="rootcert"></a>创建自签名根证书

使用 New-SelfSignedCertificate cmdlet 创建自签名根证书。 有关参数的其他信息，请参阅 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)。

1. 在运行 Windows 10 或 Windows Server 2016 的计算机上，使用提升的权限打开 Windows PowerShell 控制台。 这些示例在 Azure Cloud Shell“试用”中不起作用。 必须本地运行这些示例。
2. 使用以下示例创建自签名根证书。 以下示例创建名为“P2SRootCert”、会自动安装在“Certificates-Current User\Personal\Certificates”中的自签名根证书。 打开“certmgr.msc”或“管理用户证书”，即可查看证书。

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>生成客户端证书

在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 可以从自签名根证书生成客户端证书，导出并安装该客户端证书。 如果不安装客户端证书，身份验证会失败。 

以下步骤引导完成从自签名根证书生成客户端证书的过程。 可以从相同根证书生成多个客户端证书。 使用以下步骤生成客户端证书时，客户端证书会自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，可以导出该证书。

这些示例使用 New-SelfSignedCertificate cmdlet 生成有效期为一年的客户端证书。 有关参数的其他信息（例如为客户端证书设置其他有效期），请参阅 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)。

### <a name="example-1"></a>示例 1

如果创建自签名根证书之后没有关闭 PowerShell 控制台，请使用此示例。 此示例延续上一节的内容，并使用已声明的“$cert”变量。 如果创建自签名根证书后关闭了 PowerShell 控制台，或者要在新的 PowerShell 控制台会话中创建其他客户端证书，请使用[示例 2](#ex2) 中的步骤。

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
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. 从返回的列表中找到使用者名称，然后将该名称旁边的指纹复制到文本文件中。 以下示例显示了两个证书。 CN 名称是要从中生成子证书的自签名根证书的名称。 在本例中，该根证书为“P2SRootCert”。

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
4. 修改并运行示例以生成客户端证书。 如果在未经修改的情况下直接运行以下示例，会生成名为“P2SChildCert”的客户端证书。 如果想要为子证书指定其他名称，请修改 CN 值。 运行此示例时，请不要更改 TextExtension。 生成的客户端证书会自动安装在计算机上的“Certificates - Current User\Personal\Certificates”中。

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>导出根证书公钥（.cer）

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>导出自签名根证书和私钥以将其存储（可选）

可能需要导出自签名根证书并将它存储在安全位置作为备份。 如果需要，可以稍后在另一台计算机上安装此自签名根证书，并生成更多客户端证书。 要将自签名根证书导出为 .pfx，请选择该根证书，然后使用[导出客户端证书](#clientexport)中所述的步骤导出。

## <a name="clientexport"></a>导出客户端证书

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
