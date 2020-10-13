---
title: Azure VM 中 SQL Server 的软删除和 Azure VM 工作负荷中 SAP HANA 的软删除
description: 了解 Azure VM 中 SQL Server 的软删除以及 Azure VM 工作负荷中 SAP HANA 的软删除如何使备份更安全。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254289"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure VM 中 SQL Server 的软删除和 Azure VM 工作负荷中 SAP HANA 的软删除

Azure 备份现在为 Azure VM 中的 SQL Server 和 Azure VM 工作负荷中的 SAP HANA 提供软删除。 这是对已支持的 [Azure 虚拟机软删除方案](soft-delete-virtual-machines.md)的补充。

[软删除](backup-azure-security-feature-cloud.md)是一项可帮助保护备份数据的安全功能，即使删除了备份数据，也能予以恢复。 在使用软删除的情况下，即使恶意行动者删除了数据库的备份（或用户意外删除了备份数据），备份数据也会再保留 14 天。 这样就可以恢复该备份项，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会向客户收取任何费用。

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>使用 Azure 门户对 Azure VM 中的 SQL Server 进行软删除

>[!NOTE]
>这些说明也适用于 Azure VM 中的 SAP HANA。

1. 若要删除 SQL Server 中数据库的备份数据，必须停止备份。 在 Azure 门户中，转到恢复服务保管库，转到备份项，然后选择“停止备份”。

   ![停止备份](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. 在接下来的窗口中，会提供用于删除或保留备份数据的选项。 如果选择“删除备份数据”****，则不会永久删除数据库备份， 而是将备份数据以软删除状态保留 14 天。 删除操作将延迟到第 15 天，并在第 1 天、第 12 天和第 15 天通过定期的警报电子邮件通知用户该数据库的备份状态。

   ![删除备份数据](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. 在这 14 天内，恢复服务保管库中已软删除的项旁边会显示一个红色的“软删除”图标。

   ![已软删除的项](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. 若要还原已软删除的 DB，必须先将其取消删除。 若要取消删除，请选择已软删除的 DB，然后选择“取消删除”选项****。

   ![取消删除数据库](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   窗口中会显示一条警告，指出如果选择取消删除，将取消删除数据库的所有还原点，这些还原点可用于执行还原操作。 备份项将保留为“停止保护并保留数据”状态，备份将会暂停，备份数据将永久保留，且没有任何生效的备份策略。

   ![取消删除警告](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. 此时，还可以通过从已软删除的选定备份项中选择“还原”来还原数据****。

   ![还原 VM](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. 取消删除操作完成后，状态将恢复为“停止备份并保留数据”，然后，你可以选择“恢复备份”。**** “恢复备份”操作会恢复处于活动状态的、与定义备份和保留计划的用户所选的备份策略相关联的备份项。****

   ![恢复备份](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>使用 Azure PowerShell 对 VM 中的 SQL Server 进行软删除

>[!NOTE]
>使用 Azure PowerShell 进行软删除所需的 Az.RecoveryServices 版本最低为 2.2.0。 可使用 `Install-Module -Name Az.RecoveryServices -Force` 获取最新版本。

使用 Azure PowerShell 的步骤顺序与上面概述的 Azure 门户中的步骤顺序相同。

### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 删除备份项

使用 [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet 删除备份项。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

备份项的“DeleteState”**** 将从“NotDeleted”**** 更改为“ToBeDeleted”****。 备份数据会保留 14 天。 如果要还原删除操作，则应执行撤消-删除操作。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 撤销删除操作

首先，提取处于软删除状态（也就是即将删除）的相关备份项。

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

然后，使用 [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell cmdlet 执行撤消删除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

备份项的“DeleteState”**** 将还原为“NotDeleted”****。 但保护仍处于停止状态。 恢复备份以重新启用保护。

## <a name="how-to-disable-soft-delete"></a>如何禁用软删除

建议不要禁用此功能。 唯一应该考虑禁用软删除的情况是，你打算将受保护的项移到新保管库，需要在删除后重新进行保护，因此等不及要求的 14 天（例如在测试环境中）。有关如何禁用软删除的说明，请参阅[启用和禁用软删除](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)。

## <a name="next-steps"></a>后续步骤

- 阅读有关软删除的[常见问题解答](backup-azure-security-feature-cloud.md#frequently-asked-questions)
- 了解 [Azure 备份中的所有安全功能](security-overview.md)
