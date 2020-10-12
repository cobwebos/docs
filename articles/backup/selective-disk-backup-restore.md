---
title: 适用于 Azure 虚拟机的选择性磁盘备份和还原
description: 本文介绍如何使用 Azure 虚拟机备份解决方案进行选择性磁盘备份和还原。
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: ce7e53bc740882a819e8a21e3ac95ab47d3b876a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271369"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>适用于 Azure 虚拟机的选择性磁盘备份和还原

Azure 备份支持使用虚拟机备份解决方案同时备份 VM 中的所有磁盘（操作系统和数据）。 现在，可以使用选择性磁盘备份和还原功能备份 VM 中的一部分数据磁盘。 这样就提供了一个高效且经济的针对备份和还原需求的解决方案。 每个恢复点仅包含备份操作中包含的磁盘。 这进一步允许你在还原操作期间从给定的恢复点还原一部分磁盘。 这适用于从快照和保管库还原。

## <a name="scenarios"></a>方案

此解决方案特别适用于以下情况：

1. 如果仅将关键数据备份在一个磁盘或一部分磁盘中，并且不想备份附加到 VM 的其余磁盘，以最大程度地减少备份存储成本。
2. 如果对部分 VM 或数据使用其他备份解决方案。 例如，如果使用其他工作负荷备份解决方案备份数据库或数据，并且想对其余数据或磁盘使用 Azure VM 级备份，以使用可用的最佳功能来生成高效可靠的系统。

借助 PowerShell 或 Azure CLI，可以配置 Azure VM 的选择性磁盘备份。  借助脚本，可以通过 LUN 编号包含或排除数据磁盘。  目前，只有“仅备份 OS 磁盘”选项才具有通过 Azure 门户配置选择性磁盘备份的功能。 因此，可以使用 OS 磁盘配置 Azure VM 的备份，并排除附加到该 VM 的所有数据磁盘。

>[!NOTE]
> 默认情况下，OS 磁盘已添加到 VM 备份中，不能排除。

## <a name="using-azure-cli"></a>使用 Azure CLI

确保使用 Az CLI 2.0.80 或更高版本。 可以使用以下命令获取 CLI 版本：

```azurecli
az --version
```

登录到恢复服务保管库和 VM 所在的订阅 ID：

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>在下面的每个命令中，只需提供与保管库对应的 resourcegroup 名称（而不是对象）。

### <a name="configure-backup-with-azure-cli"></a>使用 Azure CLI 配置备份

在配置保护操作期间，需要使用 inclusion / exclusion 参数指定磁盘列表设置，以提供要在备份中包含或排除的磁盘的 LUN 编号。

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

如果 VM 与保管库不在同一个资源组中，则 ResourceGroup 指代创建保管库所在的资源组。 如下所示，请提供 VM ID 而不是 VM 名称。

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>使用 Azure CLI 修改对已备份 VM 的保护

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>在配置备份期间使用 Azure CLI 仅备份 OS 磁盘

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>在修改保护期间使用 Azure CLI 仅备份 OS 磁盘

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>使用 Azure CLI 还原磁盘

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>使用 Azure CLI 仅还原 OS 磁盘

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>使用 Azure CLI 获取受保护的项，以获取磁盘排除详细信息

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

下面显示了添加到受保护项的附加 diskExclusionProperties 参数：

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>使用 Azure CLI 获取备份作业

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

此命令有助于获取已备份磁盘和已排除磁盘的详细信息，如下所示：

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>使用 Azure CLI 列出恢复点

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

这将提供 VM 中附加和备份的磁盘数的信息。

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>使用 Azure CLI 获取恢复点

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

每个恢复点都具有包含和排除的磁盘的信息：

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>使用 Azure CLI 删除磁盘排除设置并获取受保护的项

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

执行这些命令时将看到 `"diskExclusionProperties": null`。

## <a name="using-powershell"></a>使用 PowerShell

确保使用 Azure PowerShell 3.7.0 或更高版本。

### <a name="enable-backup-with-powershell"></a>使用 PowerShell 启用备份

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>在配置备份期间使用 PowerShell 仅备份 OS 磁盘

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>使用 PowerShell 获取要在修改保护操作中传递的备份项对象

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

你需要将上述 **$item** 获取的对象传递给以下 cmdlet 中的 **– item** 参数。

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>使用 PowerShell 修改对已备份 VM 的保护

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>在修改保护期间使用 PowerShell 仅备份 OS 磁盘

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>使用 PowerShell 重置磁盘排除设置

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>使用 PowerShell 还原选择性磁盘

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>使用 PowerShell 仅还原 OS 磁盘

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>使用 Azure 门户

使用 Azure 门户，可以从 VM 备份详细信息窗格和备份作业详细信息窗格中查看包含和排除的磁盘。  在还原过程中选择要从中还原的恢复点时，可以查看该恢复点中的备份磁盘。

在此处，可以从门户的 VM 备份详细信息窗格中查看虚拟机包含和排除的磁盘：

![从备份详细信息窗格中查看包含和排除的磁盘](./media/selective-disk-backup-restore/backup-details.png)

在此处，可以从作业详细信息窗格中查看备份中包含和排除的磁盘：

![从作业详细信息窗格中查看包含和排除的磁盘](./media/selective-disk-backup-restore/job-details.png)

在还原过程中选择要从中还原的恢复点时，可以在此处查看备份的磁盘：

![在还原过程中查看备份的磁盘](./media/selective-disk-backup-restore/during-restore.png)

通过 Azure 门户为 VM 配置选择性磁盘备份体验仅限于“仅备份 OS 磁盘”选项。 若要在已备份的 VM 上使用选择性磁盘备份，或对 VM 的特定数据磁盘进行高级包含或排除操作，请使用 PowerShell 或 Azure CLI。

>[!NOTE]
>如果数据跨多个磁盘，请确保备份中包含所有从属磁盘。 如果未备份某个卷中的所有从属磁盘，则在还原期间，不会创建包含一些未备份磁盘的卷。

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Azure 门户中的“仅备份 OS 磁盘”

使用 Azure 门户启用备份时，可以选择“仅备份 OS 磁盘”选项。 因此，可以使用 OS 磁盘配置 Azure VM 的备份，并排除附加到该 VM 的所有数据磁盘。

![仅为 OS 磁盘配置备份](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>使用 Azure REST API

如[此处](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)所述，可以使用几个选定磁盘配置 Azure VM 备份，也可以修改对现有 VM 的保护，以包含/排除几个磁盘。

## <a name="selective-disk-restore"></a>选择性磁盘还原

选择性磁盘还原是启用选择性磁盘备份功能时获得的附加功能。 借助此功能，可以从恢复点中备份的所有磁盘还原选择性磁盘。 这种方法更高效，并有助于在知道需要还原哪些磁盘的情况下节省时间。

- 默认情况下，OS 磁盘包含在 VM 备份和还原中，不能排除。
- 只有在启用磁盘排除功能后创建的恢复点才支持选择性磁盘还原。
- 磁盘排除设置为“打开”的备份仅支持“磁盘还原”选项。 在这种情况下，不支持“VM 还原”或“替换现有项”还原选项。

![在还原操作期间，用于还原 VM 和替换现有项的选项不可用](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>限制

经典虚拟机和加密虚拟机不支持选择性磁盘备份功能。 因此，不支持使用 BitLocker（用于加密 Windows VM）通过 Azure 磁盘加密 (ADE) 来加密 Azure VM，也不支持对 Linux VM 使用 dm-crypt 功能。

启用了选择性磁盘备份功能的 VM 不支持用于“新建 VM”和“替换现有项”的还原选项。

目前，Azure VM 备份不支持将超磁盘或共享磁盘连接到 Vm。 在这种情况下不能使用选择性磁盘备份，这会排除磁盘并备份 VM。

## <a name="billing"></a>计费

Azure 虚拟机备份遵循现有定价模式，[此处](https://azure.microsoft.com/pricing/details/backup/)详细说明了该模式。

仅当选择使用“仅 OS 磁盘”选项进行备份时，才为 OS 磁盘计算受保护实例 (PI) 成本。  如果配置备份并选择至少一个数据磁盘，则将为附加到 VM 的所有磁盘计算 PI 成本。 系统仅基于包含磁盘计算备份存储成本，因此可以节省存储成本。 系统始终为 VM 中的所有磁盘（包含和排除的磁盘）计算快照成本。

如果已选择 "跨区域还原 (CRR) 功能"，则在排除磁盘后， [CRR 定价](https://azure.microsoft.com/pricing/details/backup/) 适用于备份存储开销。

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>受保护实例是如何在 Windows 和 Linux 中仅为 OS 磁盘备份计算 (PI) 成本？

PI 成本根据 VM) 大小使用的实际 (进行计算。

- 对于 Windows：已用空间计算基于存储操作系统的驱动器（通常为 C： ) ） (。
- 对于 Linux：已用空间计算基于装入根文件系统 (/) 的设备。

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>我仅配置了 OS 磁盘备份，原因是所有磁盘都发生了快照？

选择性磁盘备份功能可通过强化作为备份一部分的包含磁盘来节省备份保管库的存储成本。 但是，会为附加到 VM 的所有磁盘拍摄快照。 因此，会始终为 VM 中的所有磁盘计算快照成本， (包含的磁盘和已排除的磁盘) 。 有关详细信息，请参阅 [计费](#billing)。

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>无法为 Azure 虚拟机配置备份，只需要排除超磁盘或附加到 VM 的共享磁盘

选择性磁盘备份功能是在 Azure 虚拟机备份解决方案之上提供的一项功能。 目前，Azure VM 备份不支持将超磁盘或共享磁盘连接到 Vm。

## <a name="next-steps"></a>后续步骤

- [Azure VM 备份的支持矩阵](backup-support-matrix-iaas.md)
- [常见问题 - 备份 Azure VM](backup-azure-vm-backup-faq.md)
