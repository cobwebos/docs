---
title: 使用批处理 Azure Batch 安全访问 Key Vault
description: 了解如何使用 Azure Batch 以编程方式从 Key Vault 访问凭据。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192296"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地访问 Key Vault

本文介绍如何设置批处理节点，以安全访问存储在 Azure Key Vault 中的凭据。 在 Key Vault 中，无需要将管理员凭据放在一起，而是从脚本访问 Key Vault 的硬编码。 解决方案是使用证书授予批处理节点对 Key Vault 的访问权限。 只需执行几个步骤，即可为批处理实现安全密钥存储。

若要对从批处理节点 Azure Key Vault 进行身份验证，需要：

- Azure Active Directory （Azure AD）凭据
- 证书
- Batch 帐户
- 至少具有一个节点的批处理池

## <a name="obtain-a-certificate"></a>获得证书

如果还没有证书，最简单的方法是使用 `makecert` 命令行工具生成自签名证书。

通常可以在此路径中找到 `makecert`： `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以管理员身份打开命令提示符，并使用以下示例导航到 `makecert`。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下来，使用 `makecert` 工具创建名为 `batchcertificate.cer` 和 `batchcertificate.pvk`的自签名证书文件。 使用的公用名（CN）对此应用程序并不重要，但将其设置为告诉您证书的用途是非常有帮助的。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

批处理需要 `.pfx` 文件。 使用[pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)工具将 `makecert` 创建的 `.cer` 和 `.pvk` 文件转换为一个 `.pfx` 文件。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>创建服务主体

向**用户**或**服务主体**授予对 Key Vault 的访问权限。 若要以编程方式访问 Key Vault，请使用我们在上一步中创建的证书的服务主体。

有关 Azure 服务主体的详细信息，请参阅[Azure Active Directory 中的应用程序和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 服务主体必须与 Key Vault 在同一 Azure AD 租户中。

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

应用程序的 Url 并不重要，因为我们只是将其用于 Key Vault 访问。

## <a name="grant-rights-to-key-vault"></a>授予 Key Vault 的权限

在上一步中创建的服务主体需要有权从 Key Vault 检索机密。 可以通过 Azure 门户或使用以下 PowerShell 命令来授予权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>将证书分配给批处理帐户

创建 Batch 池，然后在池中的 "证书" 选项卡上分配所创建的证书。 证书现在在所有批处理节点上。

接下来，需要将证书分配给批处理帐户。 通过将证书分配给帐户，我们可以将其分配给池，然后分配给节点。 要执行此操作，最简单的方法是转到门户中的 Batch 帐户，导航到 "**证书**"，然后选择 "**添加**"。 上传[获取证书](#obtain-a-certificate)并提供密码中生成的 `.pfx` 文件。 完成后，证书将添加到列表中，你可以验证指纹。

现在，在创建 Batch 池时，可以导航到池中的**证书**，并将创建的证书分配给该池。 当你执行此操作时，请确保为存储位置选择了**LocalMachine** 。 证书将加载到池中的所有批处理节点上。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell

如果计划使用 PowerShell 脚本访问节点上的 Key Vault，则需要安装 Azure PowerShell 库。 有几种方法可以实现此目的，如果你的节点安装了 Windows Management Framework （WMF）5，则可以使用安装模块命令下载它。 如果你使用的节点没有 WMF 5，最简单的安装方法是将 Azure PowerShell `.msi` 文件与批处理文件捆绑在一起，然后将安装程序作为批处理启动脚本的第一部分进行调用。 请参阅此示例了解详细信息：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>访问密钥保管库

现在，我们的所有设置都是在批处理节点上运行的脚本中访问 Key Vault。 若要从脚本访问 Key Vault，你只需使用证书对脚本进行身份 Azure AD 验证。 若要在 PowerShell 中执行此操作，请使用以下示例命令。 为**指纹**、**应用 id** （服务主体的 ID）和**租户 ID** （服务主体所在的租户）指定相应的 GUID。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

经过身份验证后，可以像往常一样访问 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

这些是要在脚本中使用的凭据。
