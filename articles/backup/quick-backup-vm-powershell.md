---
title: Azure 快速入门 - 使用 PowerShell 备份 VM
description: 了解如何使用 Azure PowerShell 备份虚拟机
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: ea4f982409f339487cd570230ebbb75682f409ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874605"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>使用 PowerShell 在 Azure 中备份虚拟机

[Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) 模块用于从命令行或脚本创建和管理 Azure 资源。 

[Azure 备份](backup-overview.md)可备份本地计算机和应用以及 Azure VM。 本文说明如何使用 AZ 模块备份 Azure VM。 或者，可以使用 [Azure CLI](quick-backup-vm-cli.md) 或在 [Azure 门户](quick-backup-vm-portal.md)中备份 VM。

参考本快速入门可在现有的 Azure VM 上备份。 如果需要创建 VM，可以[使用 Azure PowerShell 创建 VM](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)。

本快速入门需要 Azure PowerShell AZ 模块 1.0.0 版或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>登录和注册

1. 使用 `Connect-AzAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

    ```powershell
    Connect-AzAccount
    ```
2. 首次使用 Azure 备份时，必须使用 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) 在订阅中注册 Azure 恢复服务提供程序，如下所示：

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)是一个逻辑容器，用于存储受保护资源（例如 Azure VM）的备份数据。 运行备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

创建保管库时：

- 对于资源组和位置，指定要备份的 VM 的资源组和位置。
- 如果使用此[示例脚本](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)创建了 VM，则资源组为 myResourceGroup  ，VM 为 *myVM  ，资源位于 WestEurope  区域中。
- Azure 备份会自动处理备份数据的存储。 默认情况下，保管库使用[异地冗余存储 (GRS)](../storage/common/storage-redundancy-grs.md)。 异地冗余可确保将备份数据复制到距主区域数百英里以外的辅助 Azure 区域。

现在创建保管库：


1. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) 创建保管库：

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. 使用 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) 设置保管库上下文，如下所示：

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. 使用 [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) 更改保管库的存储冗余性配置 (LRS/GRS)，如下所示：
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > 只有在没有受保管库保护的备份项的情况下，才能修改存储冗余性。

## <a name="enable-backup-for-an-azure-vm"></a>为 Azure VM 启用备份

为 Azure VM 启用备份，并指定备份策略。

- 策略定义备份运行的时间，以及备份创建恢复点应保留多长时间。
- 默认保护策略为 VM 一天运行一次备份，并将创建的恢复点保留 30 天。 可以使用此默认策略快速保护 VM。 

按如下所述启用备份：

1. 首先，使用 [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) 设置默认策略：

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. 使用 [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 启用 VM 备份。 指定策略、资源组和 VM 名称。

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>启动备份作业

备份会根据备份策略中指定的计划来运行。 还可以运行临时备份：

- 第一个初始备份作业会创建一个完整恢复点。
- 初始备份之后，每个备份作业都会创建增量恢复点。
- 增量恢复点有利于存储并具有时效性，因为它们仅传输自上次备份以来所做的更改。

若要运行临时备份，可使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)。 
- 使用 [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) 指定保管库中保存备份数据的容器。
- 要备份的每个 VM 被视为一个项。 若要启动备份作业，请使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) 获取有关 VM 的信息。

运行临时备份作业，如下所示：

1. 指定容器，获取 VM 信息，并运行备份。

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. 由于第一个备份作业会创建完整恢复点，因此可能需要等待多达 20 分钟。 如下一个过程所述来监视作业。


## <a name="monitor-the-backup-job"></a>监视备份作业

1. 运行 [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) 以监视作业状态。

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    输出类似于以下示例，该示例显示作业处于“正在进行”  状态：

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. 当作业状态是“已完成”  时，VM 受保护并存储了完整恢复点。


## <a name="clean-up-the-deployment"></a>清理部署

如果不再需要备份 VM，则可以清理它。
- 如果要尝试还原 VM，请跳过清理。
- 如果使用了现有的 VM，可以跳过最后一个 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet，以保留资源组和 VM。

禁用保护，删除还原点和保管库。 然后删除资源组和关联 VM 资源，如下所示：

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建恢复服务保管库，如何在 VM 上启用保护，以及如何创建初始恢复点。 

- [了解如何](tutorial-backup-vm-at-scale.md)在 Azure 门户中备份 VM。
- [了解如何](tutorial-restore-disk.md)快速还原 VM
