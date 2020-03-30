---
title: 使用批处理安全地访问密钥保管库 - Azure 批处理
description: 了解如何使用 Azure 批处理从密钥保管库以编程方式访问凭据。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 0134e7d92ddca9bd3b45abaf642f33de9d209b33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192296"
---
# <a name="securely-access-key-vault-with-batch"></a>使用 Batch 安全地访问 Key Vault

在本文中，您将了解如何设置 Batch 节点以安全地访问存储在 Azure 密钥保管库中的凭据。 将管理员凭据放在密钥保管库中，然后硬编码凭据以从脚本访问密钥保管库是没有意义的。 解决方案是使用授予批处理节点对密钥保管库访问权限的证书。 通过几个步骤，我们可以为 Batch 实现安全密钥存储。

要从批处理节点对 Azure 密钥保管库进行身份验证，您需要：

- Azure 活动目录 （Azure AD） 凭据
- 证书
- 批次帐户
- 具有至少一个节点的批处理池

## <a name="obtain-a-certificate"></a>获得证书

如果还没有证书，则获取证书的最简单方法是使用`makecert`命令行工具生成自签名证书。

您通常可以在此`makecert`路径中找到： `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 以管理员身份打开命令提示，并使用以下示例`makecert`导航到。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

接下来，使用`makecert`该工具创建名为`batchcertificate.cer`和`batchcertificate.pvk`的自签名证书文件。 使用的通用名称 （CN） 对于此应用程序并不重要，但将其制作出能够告诉您证书用途的内容会很有帮助。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

批处理需要文件`.pfx`。 使用[pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)工具将`.cer`和`.pvk`创建`makecert`的文件转换为单个`.pfx`文件。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>创建服务主体

对密钥保管库的访问授予**用户**或服务**主体**。 要以编程方式访问密钥保管库，请使用具有我们创建上一步步骤的证书的服务主体。

有关 Azure 服务主体的详细信息，请参阅[Azure 活动目录中的应用程序和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。

> [!NOTE]
> 服务主体必须与密钥保管库位于同一 Azure AD 租户中。

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

应用程序的 URL 并不重要，因为我们只将它们用于密钥保管库访问。

## <a name="grant-rights-to-key-vault"></a>授予密钥保管库的权限

在上一步骤中创建的服务主体需要从密钥保管库检索机密的权限。 可以通过 Azure 门户或下面的 PowerShell 命令授予权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>将证书分配给批处理帐户

创建 Batch 池，然后转到池中的证书选项卡并分配您创建的证书。 证书现在位于所有批处理节点上。

接下来，我们需要将证书分配给 Batch 帐户。 将证书分配给帐户允许将其分配给池，然后分配给节点。 最简单的方法是转到门户中的 Batch 帐户，导航到**证书**，然后选择 **"添加**"。 上传我们在`.pfx`[获取证书](#obtain-a-certificate)中生成的文件并提供密码。 完成后，证书将添加到列表中，您可以验证指纹。

现在，在创建 Batch 池时，可以导航到池中的**证书**，并将创建的证书分配给该池。 执行此操作时，请确保为商店位置选择**本地计算机**。 证书加载在池中的所有 Batch 节点上。

## <a name="install-azure-powershell"></a>安装 Azure PowerShell

如果计划使用节点上的 PowerShell 脚本访问密钥保管库，则需要安装 Azure PowerShell 库。 有几种方法可以做到这一点，如果您的节点安装了 Windows 管理框架 （WMF） 5，则可以使用安装模块命令来下载它。 如果使用的节点没有 WMF 5，则安装它的最简单方法是将 Azure PowerShell`.msi`文件与 Batch 文件捆绑在一起，然后将安装程序称为 Batch 启动脚本的第一部分。 有关详细信息，请参阅此示例：

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>访问密钥保管库

现在，我们都设置在批处理节点上运行的脚本中访问密钥保管库。 要从脚本访问密钥保管库，只需让脚本使用证书对 Azure AD 进行身份验证。 要在 PowerShell 中执行此操作，请使用以下示例命令。 为**指纹**、**应用 ID（** 服务主体的 ID）和**租户 ID（** 服务主体所在的租户）指定相应的 GUID。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

经过身份验证后，可以正常访问 KeyVault。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

这些是要在脚本中使用的凭据。
