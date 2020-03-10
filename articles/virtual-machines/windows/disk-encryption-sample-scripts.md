---
title: Azure 磁盘加密示例脚本
description: 本文是适用于 Windows Vm Microsoft Azure 磁盘加密的附录。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391487"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure 磁盘加密示例脚本 

本文提供了用于准备预加密的 Vhd 和其他任务的示例脚本。

 

## <a name="list-vms-and-secrets"></a>列出 Vm 和机密

列出订阅中的所有加密 Vm：

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
列出密钥保管库中用于加密 Vm 的所有磁盘加密机密：

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Azure 磁盘加密先决条件脚本
如果你已熟悉进行 Azure 磁盘加密的先决条件，则可以使用 [Azure 磁盘加密先决条件 PowerShell 脚本](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )。 有关此 PowerShell 脚本的用法示例，请参阅[有关加密 VM 的快速入门](disk-encryption-powershell-quickstart.md)。 可以删除脚本的某个部分中的注释（从第 211 行开始），以加密现有资源组中现有 VM 的所有磁盘。 

下表显示了可在 PowerShell 脚本中使用的参数： 

|参数|说明|必需？|
|------|------|------|
|$resourceGroupName| KeyVault 所属的资源组的名称。  如果不存在具有此名称的资源组，则会新建一个资源组。| True|
|$keyVaultName|要将加密密钥放到的 KeyVault 的名称。 如果不存在具有此名称的保管库，则会新建一个保管库。| True|
|$location|KeyVault 的位置。 请确保 KeyVault 和要加密的 VM 位于同一位置。 使用 `Get-AzLocation` 获取位置列表。|True|
|$subscriptionId|要使用的 Azure 订阅的标识符。  可以使用 `Get-AzSubscription` 获取订阅 ID。|True|
|$aadAppName|用于将机密写入 KeyVault 的 Azure AD 应用程序的名称。 如果该应用程序不存在，则会使用此名称创建新的应用程序。 如果此应用已存在，则将 aadClientSecret 参数传递给脚本。|False|
|$aadClientSecret|之前创建的 Azure AD 应用程序的客户端机密。|False|
|$keyEncryptionKeyName|KeyVault 中的可选密钥加密密钥的名称。 如果不存在具有此名称的密钥，则会新建一个密钥。|False|

## <a name="resource-manager-templates"></a>资源管理器模板

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>在不使用 Azure AD 应用的情况下加密或解密 VM

- [在现有或正在运行的 Windows VM 上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [在正在运行的 Windows VM 上禁用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>使用 Azure AD 应用加密或解密 VM（以前的版本） 
 
- [在现有或正在运行的 Windows VM 上启用磁盘加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [在正在运行的 Windows VM 上禁用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [从预加密的 VHD/存储 Blob 创建新的加密托管磁盘](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 在提供预加密 VHD 及其对应加密设置的情况下创建新的加密托管磁盘

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>准备预加密的 Windows VHD
以下部分介绍了必要操作，让你在 Azure IaaS 中准备将预加密的 Windows VHD 部署为加密 VHD。 使用该信息在 Azure Site Recovery 或 Azure 上准备和启动全新的 Windows VM (VHD)。 有关如何准备和上传 VHD 的详细信息，请参阅[上传通用化 VHD 并使用它在 Azure 中创建新的 VM](upload-generalized-managed.md)。

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>更新组策略以允许使用非 TPM 保护 OS
在“本地计算机策略” **“计算机设置”**  >  **“管理模板”**  >  **“Windows 组件”**  > 下配置名为“BitLocker 驱动器加密”的 BitLocker 组策略设置。 如下图所示，将此设置更改为“操作系统驱动器” > “启动时需要附加身份验证” > “没有兼容的 TPM 时允许 BitLocker”：

![Azure 中的 Microsoft Antimalware](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>安装 BitLocker 功能组件
对于 Windows Server 2012 或更高版本，请使用以下命令：

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

对于 Windows Server 2008 R2，请使用以下命令：

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>使用 `bdehdcfg` 为 BitLocker 准备 OS 卷
若要压缩 OS 分区并为 BitLocker 准备计算机，请根据需要执行 [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment)：

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>使用 BitLocker 保护 OS 卷
使用 [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) 命令在使用外部密钥保护程序的引导卷上启用加密。 此外将外部密钥（.bek 文件）放在外部驱动器或卷上。 下次重启后，会在系统/引导卷上启用加密。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> 使用独立的数据/资源 VHD 准备 VM，以使用 BitLocker 获取外部密钥。

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>将加密的 VHD 上传到 Azure 存储帐户
启用 DM Dm-crypt 加密后，需要将本地加密的 VHD 上传到存储帐户。
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>将预加密的 VM 的机密上传到密钥保管库
你之前获取的磁盘加密机密必须上传为密钥保管库中的机密。  这要求向将上传密钥的帐户授予 set secret 权限和 wrapkey 权限。

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>未使用 KEK 加密的磁盘加密机密
若要在密钥保管库中设置机密，请使用[AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)。 该通行短语编码为 base64 字符串，然后上传到密钥保管库。 此外，请确保在 Key Vault 中创建机密时设置以下标记。

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


在下一步中使用 `$secretUrl` 以便[在不使用 KEK 的情况下附加 OS 磁盘](#without-using-a-kek)。

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>使用 KEK 加密的磁盘加密机密
将机密上传到 Key Vault 之前，可根据需要使用密钥加密密钥对其进行加密。 先使用包装 [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) 加密使用密钥加密密钥的机密。 此包装操作的输出是 base64 URL 编码的字符串，可以使用 [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 将其作为机密上传。

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

将在下一步中使用 `$KeyEncryptionKey` 和 `$secretUrl` 以便[在使用 KEK 的情况下附加 OS 磁盘](#using-a-kek)。

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>附加 OS 磁盘时指定机密 URL

###  <a name="without-using-a-kek"></a>不使用 KEK
附加 OS 磁盘时，需要传递 `$secretUrl`。 该 URL 是在“不使用 KEK 对磁盘加密机密进行加密”部分中生成的。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>使用 KEK
附加 OS 磁盘时，传递 `$KeyEncryptionKey` 和 `$secretUrl`。 该 URL 是在“使用 KEK 加密的磁盘加密机密”部分生成的。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
