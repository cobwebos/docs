---
title: 使用 Batch 安全地访问 Key Vault
description: 了解如何使用 Azure Batch 以编程方式从 Key Vault 访问凭据。
ms.topic: how-to
ms.date: 02/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6938d0fcd2357efcf03053b0c9b2bde3954270b7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079432"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地访问 Key Vault

本文将介绍如何设置 Batch 节点，以安全地访问存储在 Azure Key Vault 中的凭据。 无需将管理员凭据放入 Key Vault，然后对凭据进行硬编码以从脚本访问 Key Vault。 解决方案是使用授予 Batch 节点对 Key Vault 的访问权限的证书。 只需执行几个步骤，即可为 Batch 实现安全密钥存储。

若要从 Batch 节点向 Azure Key Vault 进行身份验证，需要：

- 一个 Azure Active Directory (Azure AD) 凭据
- 一个证书
- 一个 Batch 帐户
- 具有至少一个节点的 Batch 池

## <a name="obtain-a-certificate"></a>获得证书

如果还没有证书，获取证书最简单的方法是使用 `makecert` 命令行工具生成自签名证书。

通常可以在以下路径中找到 `makecert`：`C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以管理员身份打开命令提示符，并使用以下示例导航到 `makecert`。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下来，使用 `makecert` 工具创建名为 `batchcertificate.cer` 和 `batchcertificate.pvk` 的自签名证书文件。 使用的公用名称 (CN) 对此应用程序并不重要，但将其设置为告诉你证书的用途是非常有帮助的。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch 需要 `.pfx` 文件。 使用 [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) 工具将 `makecert` 创建的 `.cer` 和 `.pvk` 文件转换为单个 `.pfx` 文件。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>创建服务主体

将对 Key Vault 的访问权限授予用户或服务主体。  若要以编程方式访问 Key Vault，请结合使用服务主体和我们在上一步中创建的证书。

有关 Azure 服务主体的详细信息，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 服务主体必须与 Key Vault 位于同一 Azure AD 租户中。

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

应用程序的 URL 并不重要，因为我们只是将其用于 Key Vault 访问。

## <a name="grant-rights-to-key-vault"></a>授予 Key Vault 的权限

在上一步中创建的服务主体需要权限才能从 Key Vault 检索机密。 可以通过 Azure 门户或使用以下 PowerShell 命令来授予权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>将证书分配给 Batch 帐户

创建 Batch 池，然后转到该池中的“证书”选项卡，并分配所创建的证书。 证书现在位于所有 Batch 节点上。

接下来，我们需要将证书分配给 Batch 帐户。 通过将证书分配给帐户，我们可以将其分配给池，然后分配给节点。 执行此操作最简单的方法是，转到门户中的 Batch 帐户，导航到“证书”，然后选择“添加”。  上传我们在[获取证书`.pfx`中生成的 ](#obtain-a-certificate) 文件并提供密码。 完成后，相应的证书将添加到列表，你可以验证指纹。

现在，在创建 Batch 池时，可以导航到池中的证书，并将创建的证书分配给该池。 执行此操作时，请确保选择存储位置 LocalMachine。 证书将加载到池中的所有 Batch 节点上。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell

如果计划在节点上使用 PowerShell 脚本访问 Key Vault，则需要安装 Azure PowerShell 库。 有几种方法可以实现此目的，如果你的节点安装了 Windows Management Framework (WMF) 5，则可以使用 install-module 命令下载它。 如果你使用的节点没有 WMF 5，最简单的安装方法是将 Azure PowerShell `.msi` 文件与 Batch 文件捆绑在一起，然后将安装程序作为 Batch 启动脚本的第一部分进行调用。 有关详细信息，请参阅此示例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>访问密钥保管库

现在，我们可以访问在 Batch 节点上运行的脚本中的 Key Vault。 若要从脚本访问 Key Vault，你只需使用证书对脚本针对 Azure AD 进行身份验证。 若要在 PowerShell 中执行此操作，请使用以下示例命令。 为“指纹”、“应用 ID”（服务主体的 ID）和“租户 ID”（服务主体所在的租户）指定相应的 GUID。  

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

经过身份验证后，可以像往常一样访问 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

这些是要在脚本中使用的凭据。
