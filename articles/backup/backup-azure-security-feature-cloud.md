---
title: 有助于保护云工作负荷的安全功能
description: 了解如何在 Azure 备份中使用安全功能，使备份更加安全。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 57c9fd76ae32aea49f480f2a88d8296538d8052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156066"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>有助于保护使用 Azure 备份的云工作负荷的安全功能

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防范此类攻击，Azure 备份现提供可帮助保护备份数据（即使数据已删除）的安全功能。

其中一个功能是软删除。 使用软删除，即使恶意参与者删除 VM 的备份（或意外删除备份数据），备份数据也会再保留 14 天，从而在无数据丢失时恢复备份项。 在"软删除"状态下额外保留备份数据 14 天不会给客户带来任何成本。 Azure 还使用[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)对所有静态备份的数据进行加密，以进一步保护数据。

Azure 虚拟机的软删除保护通常可用。

>[!NOTE]
>Azure VM 中的 SQL 服务器软删除和 Azure VM 工作负荷中 SAP HANA 的软删除现在在预览版中可用。<br>
>要注册预览版，请写信给我们：AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>软删除

### <a name="soft-delete-for-vms"></a>VM 的软删除

适用于 VM 的软删除可保护 VM 的备份免受意外删除。 即使在删除备份后，备份也会在软删除状态中保留 14 天。

> [!NOTE]
> 软删除只是保护已删除的备份数据。 如果删除未备份的 VM，则软删除功能不会保留数据。 应使用 Azure 备份保护所有资源，以确保能够完全复原。
>

### <a name="supported-regions"></a>支持的区域

软删除目前支持在美国中西部，东亚，加拿大中部，加拿大东部，法国中部，法国南部，韩国中部，韩国，英国南部，英国西部，澳大利亚东部，澳大利亚东南，北欧，美国西部，美国西部，美国中部，南部东亚、美国中北部、美国中南部、日本东部、日本西部、印度南部、印度中部、印度西部、美国东部2个、瑞士北部、瑞士西部以及所有国家/

### <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 门户对 VM 进行软删除

1. 要删除 VM 的备份数据，必须停止备份。 在 Azure 门户中转到你的恢复服务保管库，右键单击备份项，然后选择“停止备份”。****

   ![Azure 门户中备份项的屏幕截图](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在以下窗口中，您可以选择删除或保留备份数据。 如果选择 **"删除备份数据****"，然后停止备份**，则 VM 备份不会永久删除。 而是将备份数据以软删除状态保留 14 天。 如果选择“删除备份数据”，则会将一封“删除”警报电子邮件发送到配置的电子邮件 ID，告知用户备份数据将延期保留 14 天。**** 在第 12 天时也会发送一封警报电子邮件，告知还有两天时间可以恢复已删除的数据。 删除会推迟到第 15 天，到时数据将永久删除，同时系统会发送最后一封警报电子邮件，告知数据已永久删除。

   ![Azure 门户中“停止备份”屏幕的屏幕截图](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在这 14 天内，在恢复服务保管库中，软删除的 VM 将旁边出现一个红色的"软删除"图标。

   ![Azure 门户中处于软删除状态的 VM 的屏幕截图](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保管库中存在任何已软删除的备份项，此时无法删除该保管库。 请在永久删除了备份项，并且保管库中未留下任何处于软删除状态的项之后，尝试删除保管库。

4. 要还原软删除的 VM，必须首先取消删除它。 若要取消删除，请选择软删除的 VM，然后选择“取消删除”选项。****

   ![Azure 门户中“取消删除 VM”的屏幕截图](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   窗口中会显示一条警告，指出如果选择取消删除，将取消删除 VM 的所有还原点，这些还原点可用于执行还原操作。 VM 将保留处于"停止保护、保留数据"状态，备份暂停，备份数据永久保留，且备份策略无效。

   ![Azure 门户中“确认取消删除 VM”的屏幕截图](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此时，还可以通过从选定还原点中选择“还原 VM”来还原 VM。****  

   ![Azure 门户中“还原 VM”选项的屏幕截图](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在用户执行“恢复备份”操作后，垃圾回收器才会运行并清理已过期的恢复点。****

5. 取消删除过程完成后，状态将返回到"使用保留数据停止备份"，然后您可以选择 **"恢复备份**"。 “恢复备份”操作会恢复处于活动状态的、与定义备份和保留计划的用户所选的备份策略相关联的备份项。****

   ![Azure 门户中“恢复备份”选项的屏幕截图](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程图显示了启用软删除时备份项的不同步骤和状态：

![软删除的备份项的生命周期](./media/backup-azure-security-feature-cloud/lifecycle.png)

有关详细信息，请参阅下面的[常见问题解答](backup-azure-security-feature-cloud.md#frequently-asked-questions)部分。

### <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure PowerShell 的 VM 软删除

> [!IMPORTANT]
> 使用 Azure PS 软删除所需的 Az.RecoveryServices 版本最低为 2.2.0。 可使用 ```Install-Module -Name Az.RecoveryServices -Force``` 获取最新版本。

如上文对 Azure 门户所述，在使用 Azure PowerShell 时，步骤顺序相同。

#### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 删除备份项目

使用 [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet 删除备份项。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

备份项的“DeleteState”将从“NotDeleted”更改为“ToBeDeleted”。 备份数据会保留 14 天。 如果要还原删除操作，则应执行撤消-删除操作。

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 撤消删除操作

首先，获取处于软删除状态（即即将删除）的相关备份项。

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

备份项的“DeleteState”将还原为“NotDeleted”。 但保护仍处于停止状态。 [恢复备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)以重新启用保护。

### <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 对 VM 进行软删除

- 如[此处](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述，使用 REST API 删除备份。
- 如果用户想要撤消这些删除操作，请参阅[此处](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步骤。

## <a name="disabling-soft-delete"></a>禁用软删除

软删除在新创建的保管库上默认启用，目的是防止意外或恶意删除备份数据。  不建议禁用此功能。 应考虑禁用软删除的唯一情况是，如果您计划将受保护的项目移动到新保管库，并且无法等待删除和重新保护所需的 14 天（例如在测试环境中）。只有保管库所有者才能禁用此功能。 如果禁用此功能，以后所有删除受保护项目将导致立即删除，无法还原。 在禁用此功能之前，处于软删除状态的备份数据将保持在软删除状态 14 天。 若要立即永久删除这些项，则需先取消删除，然后再次将其删除，这样就可以永久删除它们。

### <a name="disabling-soft-delete-using-azure-portal"></a>使用 Azure 门户禁用软删除

若要禁用软删除，请执行以下步骤：

1. 在 Azure 门户中，转到保管库，然后转到 **"设置** -> **属性**"。
2. 在属性窗格中，选择 **"安全设置** -> **更新**"。  
3. 在"安全设置"窗格中，在 **"软删除"** 下，选择 **"禁用**"。

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
2. 在 Azure 门户中，转到保管库，转到**备份项目**，然后选择软删除的 VM。

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
2. 然后使用 REST API 使用[此处](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)提到的步骤禁用软删除功能。
3. 然后，使用[此处](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述的 REST API 删除备份。

## <a name="encryption"></a>加密

使用 Azure 存储加密将所有备份数据存储在云中时自动加密，这有助于您履行安全性和合规性承诺。 静态数据使用 256 位 AES 加密进行加密，这是可用的最强块密码之一，并且符合 FIPS 140-2 标准。

除了静态加密外，传输中的所有备份数据都通过 HTTPS 传输。 它始终保留在 Azure 主干网络上。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 请参阅[Azure 备份常见问题解答](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，回答有关加密的任何问题。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台管理的密钥加密备份数据

默认情况下，所有数据都使用平台管理的密钥进行加密。 您无需从末尾执行任何显式操作来启用此加密，它适用于备份到恢复服务保管库的所有工作负载。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户管理的密钥加密备份数据

备份 Azure 虚拟机时，现在可以使用您拥有和管理的密钥对数据进行加密。 Azure 备份允许您使用存储在 Azure 密钥保管库中的 RSA 密钥来加密备份。 用于加密备份的加密密钥可能与用于源的加密密钥不同。 数据使用基于 AES 256 的数据加密密钥 （DEK） 进行保护，密钥反过来又使用密钥进行保护。 这使您可以完全控制数据和键。 要允许加密，需要授予恢复服务保管库对 Azure 密钥保管库中的加密密钥的访问权限。 您可以根据需要禁用密钥或撤消访问权限。 但是，在尝试保护保管库上的任何项目之前，必须使用密钥启用加密。

>[!NOTE]
>此功能当前可用性有限。 如果您希望使用客户托管密钥加密备份数据，AskAzureBackupTeam@microsoft.com请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)并发送电子邮件至我们这里。 请注意，使用此功能的功能须经 Azure 备份服务的批准。

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>使用客户托管密钥加密的托管磁盘 VM 的备份

Azure 备份还允许您备份使用密钥进行服务器端加密的 Azure VM。 用于加密磁盘的密钥存储在 Azure 密钥保管库中并由您管理。 使用客户管理的密钥的服务器端加密不同于 Azure 磁盘加密，因为 ADE 利用 BitLocker（用于 Windows）和 DM-Crypt（用于 Linux）来执行访客内加密，SSE 对存储服务中的数据进行加密，使您能够使用任何操作系统或VM 的图像。 有关详细信息，请参阅[使用客户托管密钥加密托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

### <a name="backup-of-vms-encrypted-using-ade"></a>使用 ADE 加密的 VM 备份

使用 Azure 备份，还可以备份使用 Azure 磁盘加密加密其操作系统或数据磁盘的 Azure 虚拟机。 ADE 使用用于 Windows VM 的 BitLocker 和用于 Linux VM 的 DM-Crypt 以执行访客内加密。 有关详细信息，请参阅[使用 Azure 备份 备份备份和还原加密虚拟机](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="private-endpoints"></a>专用终结点

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>其他安全功能

### <a name="protection-of-azure-backup-recovery-points"></a>保护 Azure 备份恢复点

恢复服务保管库使用的存储帐户是隔离的，恶意用户无法对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 可通过基于角色的访问控制 (RBAC) 来控制这些管理操作。

有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="for-soft-delete"></a>对于软删除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>是否需要对每个保管库启用软删除功能？

否，默认情况下，它是为所有恢复服务保管库构建和启用的。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>是否可以配置在完成删除操作后，以软删除状态保留数据的天数？

否，在删除操作后，它固定为 14 天的额外保留时间。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>是否需要支付这额外 14 天数据保留的费用？

否。14 天额外保留是软删除功能免费附送的。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

否。需要取消删除已软删除的资源才能还原。 取消删除操作会将资源恢复到“停止保护并保留数据”状态，然后，你可以还原到任意时间点。**** 在此状态下，垃圾回收器将保持暂停状态。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>快照的生命周期是否与保管库中恢复点的生命周期相同？

是的。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何针对软删除的资源再次触发计划的备份？

先取消删除，然后执行恢复操作，即可再次保护资源。 恢复操作将关联某个备份策略，以触发具有选定保留期的计划备份。 此外，在恢复操作完成后，垃圾回收器会立即运行。 如果要从超过到期日期的恢复点执行还原，建议您在触发恢复操作之前执行还原。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保管库中存在软删除的项，我是否可以删除该保管库？

如果保管库中存在处于软删除状态的备份项目，则无法删除恢复服务保管库。 完成删除操作 14 天后，软删除的项将永久删除。 如果无法等待 14 天，则[禁用软删除](#disabling-soft-delete)，取消删除软删除的项目，然后再次删除它们以永久删除。 在确保没有受保护的项目和没有软删除项后，可以删除保管库。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>是否可以在删除后的 14 天软删除期之前删除数据？

不是。 您无法强制删除软删除的项目，这些项目会在 14 天后自动删除。 启用此安全功能是为了保护备份的数据不被意外删除或恶意删除。  你应等待 14 天，然后再在 VM 上执行任何其他操作。  软删除项目赢得'收费。  如果需要在 14 天内重新保护标记为软删除的 VM 到新保管库，请与 Microsoft 支持部门联系。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>是否可以在 PowerShell 或 CLI 中执行软删除操作？

软删除操作可以使用[PowerShell](#soft-delete-for-vms-using-azure-powershell)执行。 目前不支持 CLI。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>其他云工作负荷（例如 Azure VM 中的 SQL Server，以及 Azure VM 中的 SAP HANA）是否支持软删除？

不是。 目前只有 Azure 虚拟机支持软删除。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 备份的安全控制](backup-security-controls.md)。
