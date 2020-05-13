---
title: 通过 PowerShell 还原 Azure 文件
description: 本文介绍如何使用 Azure 备份服务和 PowerShell 还原 Azure 文件。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 63c318b66ec8f876a260b3c5b8db38bb088fb862
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201949"
---
# <a name="restore-azure-files-with-powershell"></a>通过 PowerShell 还原 Azure 文件

本文介绍如何使用 Azure PowerShell 从[Azure 备份](backup-overview.md)服务创建的还原点还原整个文件共享或特定文件。

可以还原整个文件共享，或共享中的特定文件。 可以还原到原始位置或备用位置。

> [!WARNING]
> 请确保将 PS 版本升级到最小版本的 Microsoft.recoveryservices 2.6.0。 有关详细信息，请参阅概述此更改要求的[部分](backup-azure-afs-automation.md#important-notice-backup-item-identification)。

>[!NOTE]
>Azure 备份现在支持使用 PowerShell 将多个文件或文件夹还原到原始或备用位置。 要了解如何操作，请参阅文档的[此部分](#restore-multiple-files-or-folders-to-original-or-alternate-location)。

## <a name="fetch-recovery-points"></a>提取恢复点

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) 列出已备份项的所有恢复点。

在以下脚本中：

* 变量 **$rp** 是一个数组，其中包含所选备份项在过去七天的恢复点。
* 数组按时间的反向顺序进行排序，最新的恢复点位于索引**0**处。
* 使用标准 PowerShell 数组索引选取恢复点。
* 在此示例中， **$rp [0]** 选择最新的恢复点。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

输出如下所示。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

选择相关的恢复点之后，将文件共享或文件还原到原始位置或备用位置。

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>将 Azure 文件共享还原到备用位置

使用 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 还原到所选的恢复点。 指定以下参数来标识备用位置：

* **TargetStorageAccountName**：将备份的内容还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **TargetFileShareName**：要将备份内容还原到的目标存储帐户中的文件共享。
* **TargetFolder**：要将数据还原到的文件共享上的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **ResolveConflict**：如果与还原的数据发生冲突，则为指令。 接受“覆盖”或“跳过”********。

结合如下所示的参数运行 cmdlet：

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

该命令返回一个要跟踪的、带有 ID 的作业，如以下示例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>将 Azure 文件还原到备用位置

使用 [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) 还原到所选的恢复点。 指定以下参数来标识备用位置，以及唯一标识要还原的文件。

* **TargetStorageAccountName**：将备份的内容还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **TargetFileShareName**：要将备份内容还原到的目标存储帐户中的文件共享。
* **TargetFolder**：要将数据还原到的文件共享上的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **SourceFilePath**：文件共享中要还原的文件的绝对路径（以字符串形式）。 此路径与 Get-AzStorageFile PowerShell cmdlet 中使用的路径相同****。
* **SourceFileType**：是否选择目录或文件。 接受“目录”或“文件”********。
* **ResolveConflict**：如果与还原的数据发生冲突，则为指令。 接受“覆盖”或“跳过”********。

其他参数（SourceFilePath 和 SourceFileType）只与要还原的单个文件相关。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

该命令返回一个要跟踪的、带有 ID 的作业，如前一部分中所示。

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>将 Azure 文件共享和文件还原到原始位置

在还原到原始位置时，无需指定目的地和目标相关的参数。 仅“ResolveConflict”必须提供****。

### <a name="overwrite-an-azure-file-share"></a>覆盖 Azure 文件共享

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>覆盖 Azure 文件

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>将多个文件或文件夹还原到原始或备用位置

通过将你想要还原的所有文件或文件夹的路径传递为**MultipleSourceFilePath**参数的值，使用[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)命令。

### <a name="restore-multiple-files"></a>还原多个文件

在下面的脚本中，我们将尝试还原*FileSharePage*和*mytestfile.dll*文件。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>还原多个目录

在以下脚本中，我们将尝试还原*zrs1_restore*和*还原*目录。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

输出将如下所示：

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

如果要将多个文件或文件夹还原到备用位置，请通过指定与目标位置相关的参数值来使用以上脚本，如上文将[Azure 文件还原到备用位置](#restore-an-azure-file-to-an-alternate-location)中所述。

## <a name="next-steps"></a>后续步骤

[了解如何](restore-afs.md)在 Azure 门户中还原 Azure 文件。
