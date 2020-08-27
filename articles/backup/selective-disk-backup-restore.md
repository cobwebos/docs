---
title: 针对 Azure 虚拟机的选择性磁盘备份和还原
description: 本文介绍如何使用 Azure 虚拟机备份解决方案进行选择性磁盘备份和还原。
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: 12b5b4cd35d70d8ebbd6b269e82c46984652bd07
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961986"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>针对 Azure 虚拟机的选择性磁盘备份和还原

Azure 备份支持使用虚拟机备份解决方案，将 VM 中的所有磁盘 () 的操作系统和数据一起备份。 现在，使用选择性磁盘备份和还原功能，可以备份 VM 中的数据磁盘子集。 这样就提供了一个高效且经济的针对备份和还原需求的解决方案。 每个恢复点只包含备份操作中包含的磁盘。 这会进一步使你可以在还原操作过程中从给定的恢复点还原磁盘的子集。 这适用于从快照和保管库还原。

>[!NOTE]
>针对 Azure 虚拟机的选择性磁盘备份和还原在所有区域均为公共预览版。

## <a name="scenarios"></a>方案

此解决方案特别适用于以下情况：

1. 如果要在只备份一个磁盘的关键数据或磁盘的一个子集，并且不想备份附加到 VM 的磁盘的其余部分，则将备份存储成本降至最低。
2. 如果你有 VM 或数据的一部分的其他备份解决方案。 例如，如果您使用不同的工作负荷备份解决方案来备份数据库或数据，并且您希望使用 Azure VM 级别备份来实现数据的其余部分，则使用可用的最佳功能构建有效且可靠的系统。

使用 PowerShell 或 Azure CLI，可以配置 Azure VM 的选择性磁盘备份。  使用脚本，可以使用数据磁盘的 LUN 编号来包含或排除这些磁盘。  目前，通过 Azure 门户配置选择性磁盘备份的功能 **仅限于备份 OS 磁盘** 选项。 因此，你可以配置 Azure VM 的备份和 OS 磁盘，并排除附加到它的所有数据磁盘。

>[!NOTE]
> OS 磁盘默认情况下已添加到 VM 备份，不能排除。

## <a name="using-azure-cli"></a>使用 Azure CLI

确保使用 Az CLI version 2.0.80 或更高版本。 可以通过以下命令获取 CLI 版本：

```azurecli
az --version
```

登录到恢复服务保管库和 VM 所在的订阅 ID：

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>在下面的每个命令中，只需要 **resourcegroup** 名称 (与保管库对应的对象) 。

### <a name="configure-backup-with-azure-cli"></a>将备份配置 Azure CLI

在配置保护操作期间，你需要**指定包含**  /  **排除**参数的磁盘列表设置，从而提供要在备份中包括或排除的磁盘的 LUN 编号。

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

如果 VM 不在与保管库相同的资源组中，则 **ResourceGroup** 是指在其中创建了保管库的资源组。 提供 VM ID，而不是 VM 名称，如下所示。

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>修改对已备份 Vm 的保护，并提供 Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>在配置备份期间仅备份 OS 磁盘 Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>在修改保护期间仅备份 OS 磁盘 Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>用 Azure CLI 还原磁盘

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>仅还原带有 Azure CLI 的 OS 磁盘

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>获取受保护的项以获取磁盘排除详细信息 Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

已将附加的 **diskExclusionProperties** 参数添加到受保护的项，如下所示：

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

### <a name="get-backup-job-with-azure-cli"></a>获取 Azure CLI 的备份作业

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

此命令有助于获取已备份磁盘和排除磁盘的详细信息，如下所示：

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>列出具有 Azure CLI 的恢复点

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

这会提供 VM 中附加和备份的磁盘数的信息。

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>获取 Azure CLI 的恢复点

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

每个恢复点都具有所包含和排除的磁盘的信息：

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

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>删除磁盘排除设置并 Azure CLI 获取受保护的项

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

执行这些命令时，将看到 `"diskExclusionProperties": null` 。

## <a name="using-powershell"></a>使用 PowerShell

确保使用 Azure PowerShell 版本3.7.0 或更高版本。

### <a name="enable-backup-with-powershell"></a>使用 PowerShell 启用备份

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>在用 PowerShell 配置备份期间仅备份 OS 磁盘

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>获取要通过 PowerShell 在修改保护中传递的备份项对象

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

你需要将上述 **$item** 获取的对象传递给以下 cmdlet 中的 **– item** 参数。

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>通过 PowerShell 修改已备份 Vm 的保护

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>通过 PowerShell 在修改保护期间仅备份 OS 磁盘

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>通过 PowerShell 重置磁盘排除设置

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>通过 PowerShell 还原选择性磁盘

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>仅还原带有 PowerShell 的 OS 磁盘

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>使用 Azure 门户

使用 Azure 门户，你可以从 "VM 备份详细信息" 窗格和 "备份作业详细信息" 窗格中查看包含和排除的磁盘。  在还原过程中，当你选择要从其还原的恢复点时，你可以在该恢复点中查看已备份的磁盘。

在此处，你可以从 "VM 备份详细信息" 窗格中的门户中查看虚拟机的包含和排除的磁盘：

![从 "备份详细信息" 窗格中查看包含和排除的磁盘](./media/selective-disk-backup-restore/backup-details.png)

在此处，你可以在 "作业详细信息" 窗格中查看已包含和已排除的磁盘：

![从 "作业详细信息" 窗格中查看包含和排除的磁盘](./media/selective-disk-backup-restore/job-details.png)

在此过程中，你可以在还原过程中，当你选择要从中还原的恢复点时，你可以查看备份磁盘：

![在还原过程中查看备份磁盘](./media/selective-disk-backup-restore/during-restore.png)

通过 Azure 门户为 VM 配置选择性磁盘备份体验 **仅限备份 OS 磁盘** 选项。 若要在已备份的 VM 上使用选择性磁盘备份，或者对 VM 的特定数据磁盘进行高级包含或排除，请使用 PowerShell 或 Azure CLI。

>[!NOTE]
>如果跨磁盘的数据跨越，请确保备份中包含所有相关磁盘。 如果不备份卷中的所有从属磁盘，则在还原过程中，不会创建由某些非备份磁盘组成的卷。

### <a name="backup-os-disk-only-in-the-azure-portal"></a>仅备份 OS 磁盘 Azure 门户

使用 Azure 门户启用备份时，可以选择 " **仅备份 OS 磁盘** " 选项。 因此，你可以配置 Azure VM 的备份和操作系统磁盘，并排除附加到它的所有数据磁盘。

![仅为 OS 磁盘配置备份](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="selective-disk-restore"></a>选择性磁盘还原

选择性磁盘还原是一种在启用有选择磁盘备份功能时获得的附加功能。 利用此功能，你可以从恢复点中备份的所有磁盘还原选择性磁盘。 这种方法更高效，并有助于在知道需要还原哪些磁盘的情况下节省时间。

- 默认情况下，OS 磁盘包含在 VM 备份和还原中，不能排除。
- 只有启用了磁盘排除功能后创建的恢复点才支持选择性磁盘还原。
- 磁盘 **上** 的 "排除" 设置的备份仅支持 " **磁盘还原** " 选项。 在这种情况下，不支持**VM 还原**或**替换现有**的还原选项。

![还原操作期间，用于还原 VM 和替换现有的选项不可用](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>限制

典型虚拟机和加密的虚拟机不支持选择性磁盘备份功能。 因此，使用 Azure 磁盘加密加密的 Azure Vm (ADE) 使用 BitLocker 加密 Windows VM，并且不支持适用于 Linux Vm 的 dm-crypt 功能。

对于启用了选择性磁盘备份功能的 VM，不支持用于 **创建新 vm** 和 **替换现有** vm 的还原选项。

## <a name="billing"></a>计费

Azure 虚拟机备份遵循现有的定价模型，此处将对 [此](https://azure.microsoft.com/pricing/details/backup/)进行详细说明。

仅当选择使用**Os 磁盘**选项进行备份时，才会为 OS 磁盘计算**受保护的实例 (PI) 开销**。  如果配置备份并选择至少一个数据磁盘，则会为附加到 VM 的所有磁盘计算 PI 成本。 **备份存储成本** 仅基于包含的磁盘来计算，因此你可以节省存储成本。 始终为 VM 中的所有磁盘计算**快照成本**， (包含的磁盘和已排除的磁盘) 。  

## <a name="next-steps"></a>后续步骤

- [Azure VM 备份的支持矩阵](backup-support-matrix-iaas.md)
- [常见问题 - 备份 Azure VM](backup-azure-vm-backup-faq.md)
