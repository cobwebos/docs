---
title: 使用 Azure 备份还原加密 VM 的密钥保管库密钥和机密
description: 了解如何使用 PowerShell 在 Azure 备份中还原密钥保管库密钥和机密
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b703b4511f9fefb48546b23feaa33ca7da34da1f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606097"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>使用 Azure 备份还原加密 VM 的密钥保管库密钥和机密
本文介绍如何使用 Azure VM 备份对加密 Azure VM 执行还原（当密钥和机密不在密钥保管库中时）。 如果要为还原的 VM 保留密钥（密钥加密密钥）和机密（BitLocker 加密密钥）的单独副本，也可以使用这些步骤。

## <a name="prerequisites"></a>先决条件
* **备份加密的 VM** - 已使用 Azure 备份备份加密的 Azure VM。 请参阅文章[使用 PowerShell 管理 Azure VM 的备份和还原](backup-azure-vms-automation.md)，了解有关如何备份加密的 Azure VM 的详细信息。
* **配置 Azure 密钥保管库** - 确保已存在要将密钥和机密还原到其中的密钥保管库。 请参阅文章 [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)，了解有关密钥保管库管理的详细信息。
* **还原磁盘** - 请确保已使用[PowerShell 步骤](backup-azure-vms-automation.md#restore-an-azure-vm)触发还原作业，还原加密 VM 的磁盘。 这是因为此作业将在存储帐户中生成一个 JSON 文件，其中包含要还原的加密 VM 的密钥和机密。

## <a name="get-key-and-secret-from-azure-backup"></a>从 Azure 备份获取密钥和机密

> [!NOTE]
> 为加密 VM 还原磁盘后，请确保：
> 1. 在 $details 中填写还原磁盘作业详细信息，如[“还原磁盘”部分中的 PowerShell 步骤](backup-azure-vms-automation.md#restore-an-azure-vm)所述
> 2. 只有将密钥和机密还原到密钥保管库之后，才应该从还原磁盘创建 VM。
>
>

查询已还原磁盘属性以获取作业详细信息。

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

为加密 VM 设置 Azure 存储上下文，并还原包含密钥和机密详细信息的 JSON 配置文件。

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>还原密钥
在上述目标路径中生成了 JSON 文件后，从 JSON 生成密钥 Blob 文件，并将其提供给还原密钥 cmdlet，以便将密钥 (KEK) 放回密钥保管库。

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>还原机密
使用先前生成的 JSON 文件来获取密钥名称和值，并将其他提供给设置密钥 cmdlet，以便将机密 (BEK) 放回密钥保管库。 如果 VM 通过 BEK 和 KEK 加密，请使用这些 cmdlet。

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

如果 VM 仅使用 BEK 加密，请从 JSON 生成机密 blob 文件并输入，以还原机密 cmdlet，将机密 (BEK) 放回密钥保管库。

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. 可以通过引用 $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl 的输出并使用 secrets/ 后的文本获取 $secretname 值，例如输出机密 URL 为 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163，则机密名称为 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. 标记 DiskEncryptionKeyFileName 的值与机密名称相同。
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>从还原磁盘创建虚拟机
如果已使用“Azure VM 备份”备份加密 VM，上述 PowerShell cmdlet 有助于将密钥和机密还原到密钥保管库。 完成还原后，请参阅文章[使用 PowerShell 管理 Azure VM 的备份和还原](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)，使用还原磁盘、密钥和机密创建加密 VM。

## <a name="legacy-approach"></a>传统方法
上述方法适用于所有恢复点。 然而，对于使用 BEK 和 KEK 加密的 VM，从恢复点获取密钥和机密信息的老方法对 2017 年 7 月 11 日之前的恢复点仍然有效。 使用 [PowerShell 步骤](backup-azure-vms-automation.md#restore-an-azure-vm)完成加密 VM 的还原磁盘作业后，请确保在 $rp 中填写有效值。

### <a name="restore-key"></a>还原密钥
使用下列 cmdlet 从恢复点获取密钥 (KEK) 信息，并将其提供给还原密钥 cmdlet，以将其放回密钥保管库。

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>还原机密
使用下列 cmdlet 从恢复点获取机密 (BEK) 信息，并将其提供给设置机密 cmdlet，以将其放回密钥保管库。

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. 可以通过引用 $rp1.KeyAndSecretDetails.SecretUrl 的输出和使用机密后的文本（例如输出机密 URL 为 https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 且机密名称为 B3284AAA-DAAA-4AAA-B393-60CAA848AAAA）获取 $secretname 值
> 2. 标记 DiskEncryptionKeyFileName 的值与机密名称相同。
> 3. 还原密钥并使用 [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet 后，可从密钥保管库获取 DiskEncryptionKeyEncryptionKeyURL 的值
>
>

## <a name="next-steps"></a>后续步骤
将密钥和机密还原回密钥保管库后，请参阅文章[使用 PowerShell 管理 Azure VM 的备份和还原](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)，使用还原磁盘、密钥和机密创建加密 VM。
