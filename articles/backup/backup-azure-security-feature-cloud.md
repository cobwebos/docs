---
title: Azure 备份的软删除
description: 了解如何在 Azure 备份中使用安全功能，使备份更加安全。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d7831488482ef154ce00685e513b36ed235e335e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791385"
---
# <a name="soft-delete-for-azure-backup"></a>Azure 备份的软删除

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防范此类攻击，Azure 备份现提供可帮助保护备份数据（即使数据已删除）的安全功能。

其中的一项功能是软删除。 通过软删除，即使恶意执行组件删除了备份（或意外删除了备份数据），备份数据仍将保留14天，允许恢复该备份项目，而不会丢失数据。 "软删除" 状态中的备份数据的额外14天的保留期不会对客户产生任何费用。

Azure[虚拟机](soft-delete-virtual-machines.md)中的软删除保护和 azure vm 中的[SQL server 软删除功能，适用于 azure vm 工作负荷中的 SAP HANA](soft-delete-sql-saphana-in-azure-vm.md)的软删除。

此流程图显示了启用软删除时备份项的不同步骤和状态：

![软删除的备份项的生命周期](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>启用和禁用软删除

软删除在新创建的保管库上默认启用，目的是防止意外或恶意删除备份数据。  不建议禁用此功能。 你应考虑禁用软删除的唯一情况是，你打算将受保护的项移动到新的保管库，并且无法在删除和重新保护之前等待14天（例如在测试环境中）。只有保管库所有者才能禁用此功能。 如果禁用此功能，则以后删除受保护的项将导致立即删除，而不能还原。 在禁用此功能之前，备份处于软删除状态的数据会在14天内保持为软删除状态。 若要立即永久删除这些项，则需先取消删除，然后再次将其删除，这样就可以永久删除它们。

 请记住，禁用软删除后，所有类型的工作负荷（包括 SQL server 和 SAP HANA 工作负荷）都将禁用该功能。 例如，一旦为某个订阅启用了[SQL Server/SAP HANA 预览版](https://docs.microsoft.com/azure/backup/soft-delete-sql-saphana-in-azure-vm#steps-to-enroll-in-preview)，就不可能在为同一保管库中的虚拟机启用此功能的情况下，仅对 SQL Server 或 SAP HANA db 禁用软删除。 可以创建单独的保管库以进行精细控制。

### <a name="disabling-soft-delete-using-azure-portal"></a>使用 Azure 门户禁用软删除

若要禁用软删除，请执行以下步骤：

1. 在 Azure 门户中转到保管库，然后前往 "**设置** -> " "**属性**"。
2. 在 "属性" 窗格中，选择 "**安全设置** -> **更新**"。  
3. 在 "安全设置" 窗格的 "**软删除**" 下，选择 "**禁用**"。

![禁用软删除](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>使用 Azure PowerShell 禁用软删除

> [!IMPORTANT]
> 使用 Azure PS 软删除所需的 Az.RecoveryServices 版本最低为 2.2.0。 可使用 ```Install-Module -Name Az.RecoveryServices -Force``` 获取最新版本。

若要禁用，请使用 [AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS cmdlet。

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>使用 REST API 禁用软删除

若要使用 REST API 禁用软删除功能，请参阅[此处](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)所述的步骤。

## <a name="permanently-deleting-soft-deleted-backup-items"></a>永久删除软删除的备份项

如果在禁用此功能之前备份数据处于软删除状态，则会保持软删除状态。 若要立即永久删除这些项，请取消删除，然后再次将其删除，这样就可以永久删除它们。

### <a name="using-azure-portal"></a>使用 Azure 门户

请执行这些步骤：

1. 按照步骤[禁用软删除](#enabling-and-disabling-soft-delete)。

2. 在 Azure 门户中，请前往保管库，中转到 "**备份项**"，然后选择软删除项。

   ![选择软删除项](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. 选择“撤消删除”选项****。

   ![选择“撤消删除”](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. 此时会出现一个窗口。 选择“撤消删除”****。

   ![选择“撤消删除”](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. 选择“删除备份数据”，永久删除备份数据****。

   ![选择“删除备份数据”](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. 键入备份项的名称以确认你要删除恢复点。

   ![键入备份项的名称](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 若要删除项的备份数据，请选择“删除”****。 一条通知消息将让你获悉备份数据已删除。

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

如果在禁用软删除之前删除了项，则它们将处于已软删除状态。 若要立即删除它们，需要反转删除操作，然后再次执行。

确定处于已软删除状态的项。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然后反转启用软删除时执行的删除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

由于软删除现在已禁用，删除操作将导致立即删除备份数据。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>使用 REST API

如果在禁用软删除之前删除了项，则它们将处于已软删除状态。 若要立即删除它们，需要反转删除操作，然后再次执行。

1. 首先，使用[此处](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)提到的步骤撤消删除操作。
2. 然后，使用[此处](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)所述的步骤，使用 REST API 禁用软删除功能。
3. 然后，使用[此处](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述的 REST API 删除备份。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>是否需要对每个保管库启用软删除功能？

不会，它是内置的，并且默认情况下已为所有恢复服务保管库启用。

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>是否可以配置在删除操作完成后，数据将在软删除状态中保留的天数？

不可以。删除操作后，它会固定到14天的额外保留期。

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>是否需要支付这额外 14 天数据保留的费用？

不能，这14天的额外保留期作为软删除功能的一部分提供。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

否。需要取消删除已软删除的资源才能还原。 取消删除操作会将资源恢复到“停止保护并保留数据”状态，然后，你可以还原到任意时间点。**** 在此状态下，垃圾回收器将保持暂停状态。

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>快照的生命周期是否与保管库中恢复点的生命周期相同？

是的。

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何针对软删除的资源再次触发计划的备份？

撤消删除后，继续操作将再次保护该资源。 恢复操作将备份策略与选定的保留期一起触发计划的备份。 此外，在恢复操作完成后，垃圾回收器会立即运行。 如果要从超过其到期日期的恢复点执行还原，则建议在触发恢复操作之前执行此操作。

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保管库中存在软删除的项，我是否可以删除该保管库？

如果保管库中的备份项处于软删除状态，则无法删除恢复服务保管库。 完成删除操作 14 天后，软删除的项将永久删除。 如果你不能等待14天，则[禁用软删除](#enabling-and-disabling-soft-delete)，删除软删除项，并再次将其删除以永久删除。 确保不存在受保护的项，并且没有软删除的项，则可以删除该保管库。  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>是否可以在删除后的 14 天软删除期之前删除数据？

否。 不能强制删除软删除的项。 它们将在14天后自动删除。 启用此安全功能是为了保护备份的数据不被意外删除或恶意删除。  你应等待14天，然后对该项执行任何其他操作。  不会收取软删除项的费用。  如果需要在新的保管库中重新保护标记为软删除的项目，请联系 Microsoft 支持部门。

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>是否可以在 PowerShell 或 CLI 中执行软删除操作？

可以使用 PowerShell 执行软删除操作。 目前不支持 CLI。

## <a name="next-steps"></a>后续步骤

- [Azure 备份中的安全功能概述](security-overview.md)
