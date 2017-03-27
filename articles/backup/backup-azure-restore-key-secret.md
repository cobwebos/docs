---
title: "使用 Azure 备份还原加密 VM 的密钥保管库密钥和机密 | Microsoft Docs"
description: "了解如何使用 PowerShell 在 Azure 备份中还原密钥保管库密钥和机密"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: pajosh
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: ddb9e7909eb4ab97204059d21690795ceb6ff9e8
ms.lasthandoff: 03/02/2017


---
# <a name="restore-an-encrypted-virtual-machine-from-an-azure-backup-recovery-point"></a>从 Azure 备份还原点还原加密虚拟机
本文介绍如何使用 Azure VM 备份对加密 Azure VM 执行还原（当密钥和机密不在密钥保管库中时）。 如果要为还原的 VM 保留密钥（密钥加密密钥）和机密（BitLocker 加密密钥）的单独副本，也可以使用这些步骤。

## <a name="pre-requisites"></a>先决条件
1. **备份加密的 VM** - 已使用 Azure 备份备份加密的 Azure VM。 请参阅文章[使用 PowerShell 管理 Azure VM 的备份和还原](backup-azure-vms-automation.md)，了解有关如何备份加密的 Azure VM 的详细信息。
2. **配置 Azure 密钥保管库** - 确保已存在要将密钥和机密还原到其中的密钥保管库。 请参阅文章 [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)，了解有关密钥保管库管理的详细信息。

## <a name="setup-recovery-services-vault"></a>设置恢复服务保管库
使用以下步骤登录 PowerShell 并设置恢复服务保管库上下文

### <a name="log-in-to-azure-powershell"></a>登录到 Azure PowerShell
使用以下 cmdlet 登录 Azure 帐户

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>设置恢复服务保管库上下文
登录后，使用以下 cmdlet 获取可用订阅列表

```
PS C:\> Get-AzureRmSubscription
```

选择其中有可用资源的订阅

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

使用恢复服务保管库（其中已对加密 VM 启用备份）设置保管库上下文

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>获取还原点
从代表加密 Azure 虚拟机的保管库中选择容器

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

使用此容器获取相应虚拟机的备份项

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

在变量 rp 中，获取所选备份项的还原点的数组

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>还原加密的虚拟机
使用以下步骤还原加密的 VM 及其密钥和机密。

### <a name="restore-key"></a>还原密钥
上文的 $rp 数组按时间反向排序，最新的还原点位于索引 0。 例如：$rp[0] 选择最新的还原点。

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> 此 cmdlet 运行成功后，在运行此 cmdlet 的计算机上的指定文件夹中生成 blob 文件。 此 blob 文件代表加密形式的密钥加密密钥。
>
>

使用以下 cmdlet 将密钥还原到密钥保管库。

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>还原机密
从上面获取的还原点还原机密数据

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> Vault.azure.net 前的文本表示原始密钥保管库名称。 secrets/ 之后的文本表示机密名称。
>
>

如果希望使用相同的机密名称，请从上面运行的 cmdlet 的输出中获取机密名称和值。 否则应更新下面的 $secretname 使用新的机密名称。

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

设置机密的标记，以防 VM 也需要还原。 标记 DiskEncryptionKeyFileName 的值应包含计划使用的机密的名称。

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> DiskEncryptionKeyFileName 的值与上面获得的机密名称相同。 还原密钥并使用 [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet 后，可从密钥保管库获取 DiskEncryptionKeyEncryptionKeyURL 的值    
>
>

将机密设置回密钥保管库

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>恢复虚拟机
如果已使用 Azure VM 备份备份加密 VM，上面的 PowerShell cmdlet 有助于将密钥和机密还原到密钥保管库。 完成还原后，请参阅文章[使用 PowerShell 管理 Azure VM 的备份和还原](backup-azure-vms-automation.md)来还原加密 VM。

