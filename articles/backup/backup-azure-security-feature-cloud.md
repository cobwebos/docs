---
title: 有助于保护云工作负荷的安全功能
description: 了解如何在 Azure 备份中使用安全功能，使备份更加安全。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668740"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>有助于保护使用 Azure 备份的云工作负荷的安全功能

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防范此类攻击，Azure 备份现提供可帮助保护备份数据（即使数据已删除）的安全功能。

其中的一项功能是软删除。 在使用软删除的情况下，即使恶意行动者删除了 VM 的备份（或用户意外删除了备份数据），备份数据也仍会保留 14 天，因此可以恢复该备份项，而不会丢失数据。 "软删除" 状态中的备份数据的额外14天的保留期不会对客户产生任何费用。 Azure 还使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)来加密所有已备份的静态数据，以进一步保护数据。

Azure 虚拟机的软删除保护功能已推出正式版。

>[!NOTE]
>针对 Azure VM 中 SQL 服务器的软删除以及针对 Azure VM 工作负荷中 SAP HANA 的软删除现已推出预览版。<br>
>若要注册预览版，请向我们写信AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>软删除

### <a name="soft-delete-for-vms"></a>VM 的软删除

Vm 软删除保护 Vm 的备份不被意外删除。 即使在删除备份后，它们仍将保留14天的软删除状态。

> [!NOTE]
> 软删除只是保护已删除的备份数据。 如果删除未备份的 VM，则软删除功能不会保留数据。 应使用 Azure 备份保护所有资源，以确保能够完全复原。
>

### <a name="supported-regions"></a>支持的区域

目前，美国西部当前支持软删除。东亚、加拿大中部、加拿大东部、法国中部、法国南部、韩国中部、韩国南部、英国南部、英国西部、澳大利亚东部、澳大利亚东南部、北欧、美国西部、2、美国中部、美国西部、美国中北部、美国中南部东亚、日本东部、日本西部、印度南部、印度中部、日本西部、印度南部、印度中部、印度西部、美国东部2、瑞士北部、瑞士西部、挪威西部、挪威东部和所有国家地区。

### <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 门户对 VM 进行软删除

1. 若要删除 VM 的备份数据，必须停止备份。 在 Azure 门户中转到你的恢复服务保管库，右键单击备份项，然后选择“停止备份”。****

   ![Azure 门户中备份项的屏幕截图](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下面的窗口中，您将可以选择是删除还是保留备份数据。 如果选择 "**删除备份数据**"，然后**停止备份**，则不会永久删除 VM 备份。 而是将备份数据以软删除状态保留 14 天。 如果选择“删除备份数据”，则会将一封“删除”警报电子邮件发送到配置的电子邮件 ID，告知用户备份数据将延期保留 14 天。**** 在第 12 天时也会发送一封警报电子邮件，告知还有两天时间可以恢复已删除的数据。 删除会推迟到第 15 天，到时数据将永久删除，同时系统会发送最后一封警报电子邮件，告知数据已永久删除。

   ![Azure 门户中“停止备份”屏幕的屏幕截图](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在14天内，在恢复服务保管库中，软删除的 VM 旁边会显示一个红色的 "软删除" 图标。

   ![Azure 门户中处于软删除状态的 VM 的屏幕截图](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保管库中存在任何已软删除的备份项，此时无法删除该保管库。 请在永久删除了备份项，并且保管库中未留下任何处于软删除状态的项之后，尝试删除保管库。

4. 若要还原软删除的 VM，必须先将其删除。 若要取消删除，请选择软删除的 VM，然后选择“取消删除”选项。****

   ![Azure 门户中“取消删除 VM”的屏幕截图](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   窗口中会显示一条警告，指出如果选择取消删除，将取消删除 VM 的所有还原点，这些还原点可用于执行还原操作。 VM 将保留为 "停止保护并保留数据" 状态，备份暂停，备份数据永久保留，无备份策略生效。

   ![Azure 门户中“确认取消删除 VM”的屏幕截图](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此时，还可以通过从选定还原点中选择“还原 VM”来还原 VM。****  

   ![Azure 门户中“还原 VM”选项的屏幕截图](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在用户执行“恢复备份”操作后，垃圾回收器才会运行并清理已过期的恢复点。****

5. 完成删除操作后，状态将返回到 "停止备份并保留数据"，然后可以选择 "**恢复备份**"。 “恢复备份”操作会恢复处于活动状态的、与定义备份和保留计划的用户所选的备份策略相关联的备份项。****

   ![Azure 门户中“恢复备份”选项的屏幕截图](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程图显示了启用软删除时备份项的不同步骤和状态：

![软删除的备份项的生命周期](./media/backup-azure-security-feature-cloud/lifecycle.png)

有关详细信息，请参阅下面的[常见问题解答](backup-azure-security-feature-cloud.md#frequently-asked-questions)部分。

### <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure PowerShell 的 Vm 软删除

> [!IMPORTANT]
> 使用 Azure PS 软删除所需的 Az.RecoveryServices 版本最低为 2.2.0。 可使用 ```Install-Module -Name Az.RecoveryServices -Force``` 获取最新版本。

如上所述，在 Azure 门户中，同时使用 Azure PowerShell 时，步骤顺序是相同的。

#### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 删除备份项

使用 [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet 删除备份项。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

备份项的“DeleteState”将从“NotDeleted”更改为“ToBeDeleted”。 备份数据会保留 14 天。 如果要还原删除操作，则应执行撤消-删除操作。

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 撤消删除操作

首先，提取处于软删除状态（即要删除）的相关备份项。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然后，使用 [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS cmdlet 执行撤消-删除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

备份项的“DeleteState”将还原为“NotDeleted”。 但保护仍处于停止状态。 [继续备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)以重新启用保护。

### <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 对 VM 进行软删除

- 如[此处](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述，使用 REST API 删除备份。
- 如果用户想要撤消这些删除操作，请参阅[此处](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步骤。

## <a name="disabling-soft-delete"></a>禁用软删除

软删除在新创建的保管库上默认启用，目的是防止意外或恶意删除备份数据。  不建议禁用此功能。 你应考虑禁用软删除的唯一情况是，你打算将受保护的项移动到新的保管库，并且无法在删除和重新保护之前等待14天（例如在测试环境中）。只有保管库所有者才能禁用此功能。 如果禁用此功能，则以后删除受保护的项将导致立即删除，而不能还原。 在禁用此功能之前，备份处于软删除状态的数据会在14天内保持为软删除状态。 若要立即永久删除这些项，则需先取消删除，然后再次将其删除，这样就可以永久删除它们。

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

执行以下步骤:

1. 按照步骤[禁用软删除](#disabling-soft-delete)。
2. 在 Azure 门户中，请前往保管库，中转到 "**备份项**"，然后选择软删除 VM。

   ![选择已软删除的 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

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

## <a name="encryption"></a>加密

使用 Azure 存储加密存储在云中时，会自动对所有已备份的数据进行加密，这有助于你满足安全性和符合性承诺。 使用256位 AES 加密对静态数据进行加密，这是一个最强的可用块密码，并且符合 FIPS 140-2。

除静态加密外，传输中的所有备份数据都通过 HTTPS 传输。 它始终保留在 Azure 主干网络中。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 请参阅[Azure 备份常见问题解答](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，回答有关加密的任何问题。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台托管密钥加密备份数据

默认情况下，使用平台托管密钥对所有数据进行加密。 无需执行任何显式操作即可启用此加密，并将其应用于要备份到恢复服务保管库的所有工作负荷。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户管理的密钥加密备份数据

备份 Azure 虚拟机时，你现在可以使用自己拥有和管理的密钥来加密数据。 Azure 备份允许你使用存储在 Azure Key Vault 中的 RSA 密钥对备份进行加密。 用于加密备份的加密密钥可能不同于用于源的加密密钥。 使用基于 AES 256 的数据加密密钥（DEK）保护数据，进而使用密钥进行保护。 这使你可以完全控制数据和键。 若要允许加密，需要将恢复服务保管库授予对 Azure Key Vault 中的加密密钥的访问权限。 你可以根据需要禁用密钥或撤销访问权限。 但是，在尝试保护保管库中的任何项之前，必须使用密钥来启用加密。

>[!NOTE]
>此功能目前的可用性有限。 如果你想要使用客户托管密钥加密备份数据，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)并向我们发送电子邮件。 AskAzureBackupTeam@microsoft.com 请注意，若要使用此功能，可以从 Azure 备份服务进行审批。

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>使用客户管理的密钥备份已加密的托管磁盘 Vm

Azure 备份还允许备份使用密钥进行服务器端加密的 Azure Vm。 用于对磁盘进行加密的密钥存储在 Azure Key Vault 中，并由你进行管理。 使用客户托管密钥的服务器端加密不同于 Azure 磁盘加密，因为 ADE 利用 BitLocker （适用于 Windows）和 DM Dm-crypt （适用于 Linux）来执行来宾内加密，因此，SSE 对存储服务中的数据进行加密，使你能够为 Vm 使用任何 OS 或映像。 有关更多详细信息，请参阅[通过客户托管的密钥加密托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

### <a name="backup-of-vms-encrypted-using-ade"></a>备份使用 ADE 加密的 Vm

使用 Azure 备份，还可以备份使用 Azure 磁盘加密来加密操作系统或数据磁盘的 Azure 虚拟机。 ADE 使用适用于 Windows Vm 的 BitLocker 和 Dm-crypt for Linux Vm 的 DM 来执行来宾内加密。 有关详细信息，请参阅[备份和还原已加密的虚拟机和 Azure 备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="private-endpoints"></a>专用终结点

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>其他安全功能

### <a name="protection-of-azure-backup-recovery-points"></a>保护 Azure 备份恢复点

恢复服务保管库使用的存储帐户是隔离的，恶意用户无法对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 可通过基于角色的访问控制 (RBAC) 来控制这些管理操作。

有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="for-soft-delete"></a>对于软删除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>是否需要对每个保管库启用软删除功能？

不会，它在默认情况下为所有恢复服务保管库生成并启用。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>是否可以配置在完成删除操作后，以软删除状态保留数据的天数？

不可以。删除操作后，它会固定到14天的额外保留期。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>是否需要支付这额外 14 天数据保留的费用？

否。14 天额外保留是软删除功能免费附送的。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

否。需要取消删除已软删除的资源才能还原。 取消删除操作会将资源恢复到“停止保护并保留数据”状态，然后，你可以还原到任意时间点。**** 在此状态下，垃圾回收器将保持暂停状态。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>快照的生命周期是否与保管库中恢复点的生命周期相同？

是的。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何针对软删除的资源再次触发计划的备份？

先取消删除，然后执行恢复操作，即可再次保护资源。 恢复操作将关联某个备份策略，以触发具有选定保留期的计划备份。 此外，在恢复操作完成后，垃圾回收器会立即运行。 如果要从超过其到期日期的恢复点执行还原，则建议在触发恢复操作之前执行此操作。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保管库中存在软删除的项，我是否可以删除该保管库？

如果保管库中的备份项处于软删除状态，则无法删除恢复服务保管库。 完成删除操作 14 天后，软删除的项将永久删除。 如果你不能等待14天，则[禁用软删除](#disabling-soft-delete)，删除软删除项，并再次将其删除以永久删除。 确保不存在受保护的项，并且没有软删除的项，则可以删除该保管库。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>是否可以在删除后的 14 天软删除期之前删除数据？

不能。 不能强制删除软删除项，14天后会自动删除这些项。 启用此安全功能是为了保护备份的数据不被意外删除或恶意删除。  你应等待 14 天，然后再在 VM 上执行任何其他操作。  将对软删除项进行收费。  如果需要将14天内标记为软删除的 Vm 重新保护到新的保管库，请联系 Microsoft 支持部门。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>是否可以在 PowerShell 或 CLI 中执行软删除操作？

可以使用[PowerShell](#soft-delete-for-vms-using-azure-powershell)执行软删除操作。 目前不支持 CLI。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>其他云工作负荷（例如 Azure VM 中的 SQL Server，以及 Azure VM 中的 SAP HANA）是否支持软删除？

不能。 目前只有 Azure 虚拟机支持软删除。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 备份的安全控制](backup-security-controls.md)。
