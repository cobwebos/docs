---
title: "Azure 快速入门 - 使用 PowerShell 备份 VM | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 备份虚拟机"
services: backup, virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines-windows
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 1/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0e9615ca52fe981f49eb85f68cd1ab0204406bfc
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>使用 PowerShell 在 Azure 中备份虚拟机
Azure PowerShell 模块用于从命令行或脚本创建和管理 Azure 资源。 可以通过定期创建备份来保护数据。 Azure 备份可创建恢复点，这些恢复点可存储在异地冗余的恢复保管库中。 本文详细介绍如何使用 Azure PowerShell 模块备份虚拟机 (VM)。 也可以使用 [Azure CLI](quick-backup-vm-cli.md) 或 [Azure 门户](quick-backup-vm-portal.md)执行这些步骤。

参考本快速入门可在现有的 Azure VM 上备份。 如果需要创建 VM，可以[使用 Azure PowerShell 创建 VM](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)。

本快速入门需要 Azure PowerShell 模块 4.4 版或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。


## <a name="log-in-to-azure"></a>登录 Azure
使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```

首次使用 Azure 备份时，必须使用 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 在订阅中注册 Azure 恢复服务提供程序。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vaults"></a>创建恢复服务保管库
恢复服务保管库是一个逻辑容器，用于存储每个受保护资源（例如 Azure VM）的备份数据。 运行受保护资源的备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

使用 [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) 创建恢复服务保管库。 指定与想要保护的 VM 相同的资源组和位置。 如果使用[示例脚本](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)创建了 VM，则资源组名为 *myResourceGroup*，VM 名为 *myVM*，资源位于 *WestEurope* 位置。

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

默认情况下，保管库是针对异地冗余存储设置的。 为了进一步保护数据，此存储冗余级别可确保将备份数据复制到距离主要区域数百英里以外的 Azure 次要区域。

若要在剩余的步骤中使用此保管库，请使用 [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext) 设置保管库上下文

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>为 Azure VM 启用备份
创建并使用策略来定义备份作业的运行时间以及恢复点的存储期限。 默认保护策略每天运行备份作业，并将恢复点保留 30 天。 可以使用这些默认策略值来快速保护 VM。 首先，使用 [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy) 设置默认策略：

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

若要为 VM 启用备份保护，请使用 [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection)。 指定要使用的策略，再指定要保护的资源组和 VM：

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>启动备份作业
若要立即开始备份而不是等待默认策略根据计划的时间运行作业，请使用 [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)。 这第一个备份作业会创建完整恢复点。 此初始备份后的每个备份作业会创建增量恢复点。 增量恢复点有利于存储并具有时效性，因为它们仅传输自上次备份以来所做的更改。

在下面一组命令中，使用 [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer) 在恢复服务保管库中指定了一个用于保存备份数据的容器。 要备份的每个 VM 被视为一个项。 若要启动备份作业，请使用 [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem) 获取有关 VM 项的信息。

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

由于这第一个备份作业需要创建完整恢复点，因此，该过程最长可能需要花费 20 分钟。


## <a name="monitor-the-backup-job"></a>监视备份作业
若要监视备份作业的状态，请使用 [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)：

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

输出如以下示例所示，其中显示备份作业的状态为“正在进行”：

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

当备份作业的“状态”报告为“已完成”时，表示 VM 已受恢复服务的保护，并且已存储完整的恢复点。


## <a name="clean-up-deployment"></a>清理部署
如果不再需要，可以在 VM 上禁用保护，删除还原点和恢复服务保管库，然后删除资源组和关联的 VM 资源。 如果使用了现有的 VM，可以跳过最后一个 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) cmdlet，以保留资源组和 VM。

如果想要继续学习有关如何还原 VM 数据的备份教程，请跳过本部分中的步骤，并转到[后续步骤](#next-steps)。 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何创建恢复服务保管库，如何在 VM 上启用保护，以及如何创建初始恢复点。 若要详细了解 Azure 备份和恢复服务，请继续学习其他教程。

> [!div class="nextstepaction"]
> [备份多个 Azure VM](./tutorial-backup-vm-at-scale.md)
