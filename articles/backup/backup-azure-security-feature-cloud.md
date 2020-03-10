---
title: 用于帮助保护云工作负荷的安全功能
description: 了解如何在 Azure 备份中使用安全功能，使备份更安全。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 3435b9455af3362cdce2dceb20e183a8b05a15dd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395556"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>有助于保护使用 Azure 备份的云工作负荷的安全功能

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防止此类攻击，Azure 备份现在提供了安全功能来帮助保护备份数据，即使在删除后也是如此。

其中一项功能是软删除。 使用软删除，即使恶意执行组件删除了 VM 的备份（或意外删除了备份数据），备份数据仍将保留14天，允许恢复该备份项目，而不会丢失数据。 "软删除" 状态中的备份数据的额外14天的保留期不会对客户产生任何费用。 Azure 还使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)对静态中所有已备份的数据进行加密，以便进一步保护数据。

Azure 虚拟机的软删除保护已正式发布。

>[!NOTE]
>Azure vm 中的 SQL server 软删除和 Azure VM 工作负荷中 SAP HANA 的软删除现已在预览版中提供。<br>
>若要注册预览版，请在 AskAzureBackupTeam@microsoft.com 写信

## <a name="soft-delete"></a>软删除

### <a name="soft-delete-for-vms"></a>Vm 的软删除

Vm 软删除保护 Vm 的备份不被意外删除。 即使在删除备份后，它们仍将保留14天的软删除状态。

> [!NOTE]
> 软删除仅保护已删除的备份数据。 如果在没有备份的情况下删除 VM，软删除功能不会保留数据。 所有资源都应通过 Azure 备份进行保护，以确保完全恢复能力。
>

### <a name="supported-regions"></a>支持的区域

目前在美国西部、东亚、加拿大中部、加拿大东部、法国中部、法国南部、韩国中部、韩国南部、英国南部、英国西部、澳大利亚东部、澳大利亚东南部、北欧、美国西部、西2、美国中部、美国西部东亚、美国中北部、美国中南部、日本东部、日本西部、印度南部、印度中部、印度西部、美国东部2、瑞士北部、瑞士西部和所有国家地区。

### <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 门户的 Vm 软删除

1. 若要删除 VM 的备份数据，必须停止备份。 在 Azure 门户中，请参阅恢复服务保管库，右键单击备份项，然后选择 "**停止备份**"。

   ![Azure 门户备份项的屏幕截图](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下面的窗口中，您将可以选择是删除还是保留备份数据。 如果选择 "**删除备份数据**"，然后**停止备份**，则不会永久删除 VM 备份。 相反，备份数据将以软删除状态保留14天。 如果选择了 "**删除备份数据**"，则会将 "删除电子邮件" 警报发送到配置的电子邮件 ID，通知用户14天的备份数据保留期为14天。 此外，在第12天发送一封电子邮件警报，它会向你发送两天时间，指出已删除的数据恢复。 删除会推迟到第15天的时间，永久删除将发生，并发送最终的电子邮件警报，告知永久删除数据。

   ![Azure 门户的屏幕截图，停止备份屏幕](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在14天内，在恢复服务保管库中，软删除的 VM 旁边会显示一个红色的 "软删除" 图标。

   ![处于软删除状态的 Azure 门户、VM 的屏幕截图](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保管库中存在任何软删除的备份项，则无法在该时间删除该保管库。 请在永久删除备份项后尝试删除保管库，并在保管库中保留未处于软删除状态的项目。

4. 若要还原软删除的 VM，必须先将其删除。 若要删除，请选择软删除 VM，然后选择 "**撤消**删除" 选项。

   ![Azure 门户，删除 VM 的屏幕截图](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   将出现一条警告消息，指出如果选择了 "删除删除"，则会删除 VM 的所有还原点，并可将其用于执行还原操作。 VM 将保留为 "停止保护并保留数据" 状态，备份暂停，备份数据永久保留，无备份策略生效。

   ![Azure 门户的屏幕截图，确认撤消删除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此时，你还可以通过从选定的还原点选择 "**还原 vm** " 来还原 vm。  

   ![Azure 门户，Restore VM 选项的屏幕截图](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在用户执行**恢复备份**操作后，垃圾回收器才会运行并清除过期的恢复点。

5. 完成删除操作后，状态将返回到 "停止备份并保留数据"，然后可以选择 "**恢复备份**"。 **恢复备份**操作返回处于活动状态的备份项，该备份项与定义备份和保留计划的用户选择的备份策略相关联。

   ![Azure 门户，恢复备份选项的屏幕截图](./media/backup-azure-security-feature-cloud/resume-backup.png)

启用软删除后，此流程图显示备份项的不同步骤和状态：

![软删除备份项的生命周期](./media/backup-azure-security-feature-cloud/lifecycle.png)

有关详细信息，请参阅[下面的常见问题](backup-azure-security-feature-cloud.md#frequently-asked-questions)部分。

### <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure Powershell 软删除 Vm

> [!IMPORTANT]
> 使用 Azure PS 软删除所需的 Microsoft.recoveryservices 版本为 min 2.2.0。 使用 ```Install-Module -Name Az.RecoveryServices -Force``` 获取最新版本。

如上所述 Azure 门户，使用 Azure Powershell 时，步骤顺序是相同的。

#### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure Powershell 删除备份项

使用[AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet 删除备份项。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

备份项的 "DeleteState" 将从 "NotDeleted" 更改为 "ToBeDeleted"。 备份数据将保留14天。 如果要还原删除操作，则应执行撤消删除操作。

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure Powershell 撤销删除操作

首先，提取处于软删除状态（即要删除）的相关备份项。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然后，使用[AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS cmdlet 执行撤消删除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

备份项的 "DeleteState" 将恢复为 "NotDeleted"。 但保护仍处于停止状态。 你需要[恢复备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)以重新启用保护。

### <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 的 Vm 软删除

- 如[此处](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述，使用 REST API 删除备份。
- 如果用户想要撤消这些删除操作，请参阅[此处](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步骤。

## <a name="disabling-soft-delete"></a>禁用软删除

默认情况下，在新创建的保管库上启用软删除，以防止意外或恶意删除备份数据。  不建议禁用此功能。 你应考虑禁用软删除的唯一情况是，如果你计划将受保护的项移动到新的保管库，并且无法在删除和重新保护之前等待14天（例如在测试环境中）。只有保管库所有者才能禁用此功能。 如果禁用此功能，则以后删除受保护的项将导致立即删除，而不能还原。 在禁用此功能之前，备份处于软删除状态的数据会在14天内保持为软删除状态。 如果希望立即永久删除这些文件，则需要取消删除并再次将其删除，以使其永久删除。

### <a name="disabling-soft-delete-using-azure-portal"></a>使用 Azure 门户禁用软删除

若要禁用软删除，请执行以下步骤：

1. 在 Azure 门户中转到保管库，然后在 "**设置**" -> **属性**"。
2. 在 "属性" 窗格中，选择 "**安全设置**" -> **更新**。  
3. 在 "安全设置" 窗格的 "**软删除**" 下，选择 "**禁用**"。

![禁用软删除](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>使用 Azure Powershell 禁用软删除

> [!IMPORTANT]
> 使用 Azure PS 软删除所需的 Microsoft.recoveryservices 版本为 min 2.2.0。 使用 ```Install-Module -Name Az.RecoveryServices -Force``` 获取最新版本。

若要禁用，请使用[AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS cmdlet。

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

在禁用此功能之前，以软删除状态备份数据，将保持软删除状态。 如果希望立即永久删除这些文件，请取消删除并再次将其删除，以使其永久删除。

### <a name="using-azure-portal"></a>使用 Azure 门户

执行以下步骤:

1. 按照以下步骤[禁用软删除](#disabling-soft-delete)。
2. 在 Azure 门户中，请前往保管库，中转到 "**备份项**"，然后选择软删除 VM。

![选择软删除 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. 选择 "**撤消删除**" 选项。

![选择撤消删除](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. 将出现一个窗口。 选择 "**删除**"。

![选择删除](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. 选择 "**删除备份数据**" 以永久删除备份数据。

![选择 "删除备份数据"](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. 键入备份项的名称以确认你要删除恢复点。

![键入备份项的名称](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 若要删除项的备份数据，请选择 "**删除**"。 一条通知消息将让你获悉备份数据已删除。

### <a name="using-azure-powershell"></a>使用 Azure Powershell

如果在禁用软删除之前删除了项目，则这些项目将处于软删除状态。 若要立即删除它们，删除操作需要反向，然后再次执行。

确定处于软删除状态的项。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然后，撤消启用软删除时执行的删除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

由于软删除现在已禁用，因此删除操作将导致立即删除备份数据。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>使用 REST API

如果在禁用软删除之前删除了项目，则这些项目将处于软删除状态。 若要立即删除它们，删除操作需要反向，然后再次执行。

1. 首先，请按照[此处](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步骤撤消删除操作。
2. 然后，使用[此处](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)所述的步骤，使用 REST API 禁用软删除功能。
3. 然后使用[此处](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述的 REST API 删除备份。

## <a name="encryption"></a>加密

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>使用 Microsoft 托管密钥加密备份数据

使用 Azure 存储加密自动对备份数据进行加密。 加密可保护你的数据，并可帮助你满足组织的安全性和符合性承诺。 数据是以透明方式加密和解密的，使用256位 AES 加密，这是可用的最强大的块密码之一，并且符合 FIPS 140-2。 Azure 存储加密类似于 Windows 上的 BitLocker 加密。

在 Azure 中，Azure 存储和保管库之间传输的数据受 HTTPS 的保护。 此数据保留在 Azure 主干网络上。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 请参阅[Azure 备份常见问题解答](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，回答有关加密的任何问题。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户托管密钥加密备份数据

备份 Azure 虚拟机时，还可以选择使用 Azure Key Vault 中存储的加密密钥对恢复服务保管库中的备份数据进行加密。

>[!NOTE]
>此功能目前正在提前使用。 如果要使用客户托管密钥加密备份数据，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)。 请注意，若要使用此功能，可以从 Azure 备份服务进行审批。

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>备份使用客户托管密钥加密的托管磁盘 VM

Azure 备份允许你备份包含使用客户托管密钥加密的磁盘的 Azure 虚拟机。 有关详细信息，请参阅[采用客户托管密钥加密托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

### <a name="backup-of-encrypted-vms"></a>备份加密的 Vm

可以使用 Azure 备份服务来备份和还原包含加密磁盘的 Windows 或 Linux Azure 虚拟机（Vm）。 有关说明，请参阅[利用 Azure 备份来备份和还原加密的虚拟机](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="other-security-features"></a>其他安全功能

### <a name="protection-of-azure-backup-recovery-points"></a>保护 Azure 备份恢复点

恢复服务保管库使用的存储帐户是独立的，用户不能出于任何恶意目的对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 这些管理操作通过基于角色的访问控制（RBAC）进行控制。

有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="for-soft-delete"></a>用于软删除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>是否需要在每个保管库上启用软删除功能？

不会，它在默认情况下为所有恢复服务保管库生成并启用。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>是否可以配置在删除操作完成后，数据将在软删除状态中保留的天数？

不可以。删除操作完成后，它会固定到14天的额外保留期。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>是否需要支付额外的14天保留期的成本？

不能，这14天的额外保留期作为软删除功能的一部分免费提供。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

不需要，你需要撤消删除软删除的资源才能还原。 "撤消删除" 操作会将资源恢复为 "停止保护"，并将 "**保留数据" 状态**恢复到任意时间点。 在此状态下，垃圾回收器将保持暂停状态。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>我的快照是否与保管库中的恢复点遵循相同的生命周期？

是的。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何对软删除的资源再次触发计划的备份？

删除后的恢复操作将再次保护该资源。 恢复操作将备份策略与选定的保留期一起触发计划的备份。 另外，当恢复操作完成后，垃圾回收器就会立即运行。 如果要从超过其到期日期的恢复点执行还原，则建议在触发恢复操作之前执行此操作。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保管库中有软删除的项，是否可以删除保管库？

如果保管库中的备份项处于软删除状态，则无法删除恢复服务保管库。 删除操作后的14天内，将永久删除软删除的项。 如果你不能等待14天，则[禁用软删除](#disabling-soft-delete)，删除软删除项，并再次将其删除以永久删除。 确保不存在受保护的项，并且没有软删除的项，则可以删除该保管库。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>删除后，是否可以删除早于14天软删除期的数据？

不是。 不能强制删除软删除项，14天后会自动删除这些项。 启用此安全功能是为了保护备份的数据不被意外删除或恶意删除。  你应等待14天，然后再在 VM 上执行任何其他操作。  不会向软删除的项目收费。  如果需要将14天内标记为软删除的 Vm 重新保护到新的保管库，请联系 Microsoft 支持部门。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>是否可以在 PowerShell 或 CLI 中执行软删除操作？

可以使用[Powershell](#soft-delete-for-vms-using-azure-powershell)执行软删除操作。 目前，CLI 不受支持。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>其他云工作负荷（如 Azure Vm 中的 SQL Server 和 Azure Vm 中 SAP HANA）支持软删除？

不是。 目前仅支持 Azure 虚拟机的软删除。

## <a name="next-steps"></a>后续步骤

- 了解[Azure 备份的安全控制](backup-security-controls.md)。
