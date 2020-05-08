---
title: Azure VM 中的 SQL server 软删除，Azure VM 工作负荷中的 SAP HANA
description: 了解 Azure VM 中的 SQL server 软删除以及 Azure VM 工作负载中 SAP HANA 如何使备份更安全。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f1e3ecae5d643b8e32f8f4f07808d56cdc421163
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791368"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure VM 中的 SQL server 软删除，Azure VM 工作负荷中的 SAP HANA

Azure 备份现在为 Azure VM 中的 SQL server 和 Azure VM 工作负荷中的 SAP HANA 提供软删除。 这是对已支持的[Azure 虚拟机软删除方案](soft-delete-virtual-machines.md)的补充。

[软删除](backup-azure-security-feature-cloud.md)是一项安全功能，可帮助在删除后保护备份数据。 通过软删除，即使恶意执行组件删除了数据库的备份（或意外删除了备份数据），备份数据仍将保留14天。 这样就可以在不丢失数据的情况下恢复该备份项。 在 "软删除" 状态下，14天的备份数据的这一额外保留期不会对客户产生任何费用。

>[!NOTE]
>为订阅启用预览后，不能仅对 SQL server 或 SAP HANA Db 禁用软删除功能，同时使其为同一保管库中的虚拟机启用。 可以创建单独的保管库以进行精细控制。

## <a name="steps-to-enroll-in-preview"></a>在预览版中注册的步骤

1. 登录到 Azure 帐户。

   ```powershell
   Login-AzureRmAccount
   ```

2. 选择要在预览版中注册的订阅：

   ```powershell
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```

3. 将此订阅注册到预览计划：

   ```powershell
   Register-AzureRMProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

4. 等待30分钟，以便将订阅注册到预览版中。

5. 若要检查状态，请运行以下 cmdlet：

   ```powershell
   Get-AzureRmProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

6. 订阅显示为 "已注册" 后，运行以下命令：

   ```powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

>[!NOTE]
>无论何时在启用软删除的订阅下创建新的保管库/保管库，都需要重新运行以下命令，以便为新创建的保管库启用此功能。<BR>
> `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>使用 Azure 门户在 Azure VM 中对 SQL server 进行软删除

>[!NOTE]
>这些说明也适用于在 Azure VM 中 SAP HANA。

1. 若要在 SQL server 中删除数据库的备份数据，必须停止备份。 在 Azure 门户中，请参阅恢复服务保管库，中转到 "备份" 项，然后选择 "**停止备份**"。

   ![停止备份](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. 在下面的窗口中，您将可以选择是删除还是保留备份数据。 如果选择 "**删除备份数据**"，则不会永久删除数据库备份。 而是将备份数据以软删除状态保留 14 天。 在第15天、第15天和第15天发出定期提醒电子邮件的情况下，会将删除操作推迟到用户的数据库备份状态。

   ![删除备份数据](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. 在14天内，在恢复服务保管库中，软删除项旁边会显示一个红色的 "软删除" 图标。

   ![软删除项](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. 若要还原软删除的数据库，必须先将其删除。 若要删除，请选择软删除的数据库，然后选择 "**撤消**删除" 选项。

   ![删除数据库](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   将出现一条警告消息，指出如果选择了 "撤消删除"，则数据库的所有还原点将被删除，并且可用于执行还原操作。 备份项将保留在 "具有保留数据的停止保护" 状态，备份暂停，备份数据将永久保留，无备份策略生效。

   ![撤消删除警告](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. 此时，你还可以通过为所选软删除备份项选择 "**还原**" 来还原数据。

   ![还原 VM](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. 取消删除操作完成后，状态将恢复为“停止备份并保留数据”，然后，你可以选择“恢复备份”。**** “恢复备份”操作会恢复处于活动状态的、与定义备份和保留计划的用户所选的备份策略相关联的备份项。****

   ![恢复备份](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>使用 Azure PowerShell 对 VM 中的 SQL server 进行软删除

>[!NOTE]
>使用 Azure PowerShell 软删除所需的 Microsoft.recoveryservices 版本是最小2.2.0。 可使用 `Install-Module -Name Az.RecoveryServices -Force` 获取最新版本。

使用 Azure PowerShell 的步骤顺序与上文概述的 Azure 门户相同。

### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 删除备份项

使用 [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet 删除备份项。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

备份项的**DeleteState**将从**NotDeleted**更改为**ToBeDeleted**。 备份数据会保留 14 天。 如果要还原删除操作，则应执行撤消-删除操作。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 撤消删除操作

首先，提取处于软删除状态（即要删除）的相关备份项。

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

然后，使用 [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.8.0) PS cmdlet 执行撤消-删除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

备份项的**DeleteState**将还原为**NotDeleted**。 但保护仍处于停止状态。 继续备份以重新启用保护。

## <a name="how-to-disable-soft-delete"></a>如何禁用软删除

不建议禁用此功能。 你应考虑禁用软删除的唯一情况是，你打算将受保护的项移动到新的保管库，并且无法在删除和重新保护之前等待14天（例如在测试环境中）。有关如何禁用软删除的说明，请参阅[启用和禁用软删除](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)。

## <a name="next-steps"></a>后续步骤

- 阅读有关[软删除的常见问题](backup-azure-security-feature-cloud.md#frequently-asked-questions)
- 了解[Azure 备份中的所有安全功能](security-overview.md)
