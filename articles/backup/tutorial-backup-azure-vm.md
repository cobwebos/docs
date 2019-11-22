---
title: 使用 PowerShell 备份多个 Azure VM
description: 本教程详细介绍了如何使用 Azure PowerShell 将多个 Azure VM 备份到恢复服务保管库。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: c290e44d8bfecfbf57be8f8e1c596a294f077229
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074712"
---
# <a name="back-up-azure-vms-with-powershell"></a>使用 PowerShell 备份 Azure VM

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本教程介绍如何使用 PowerShell 部署 [Azure 备份](backup-overview.md)以备份多个 Azure VM。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 创建恢复服务保管库并设置保管库上下文。
> * 定义备份策略
> * 应用备份策略以保护多个虚拟机
> * 对保护的虚拟机触发按需备份作业 在备份（或保护）虚拟机之前，必须完成[先决条件](backup-azure-arm-vms-prepare.md)中的步骤来准备好保护 VM 的环境。

> [!IMPORTANT]
> 本教程假定已创建资源组和 Azure 虚拟机。

## <a name="sign-in-and-register"></a>登录和注册

1. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

    ```powershell
    Connect-AzAccount
    ```

2. 首次使用 Azure 备份时，必须使用 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) 在订阅中注册 Azure 恢复服务提供程序。 如果已注册，则跳过此步骤。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)是一个逻辑容器，用于存储受保护资源（例如 Azure VM）的备份数据。 运行备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。

* 在本教程中，会在与要备份的 VM 相同的资源组和位置中创建保管库。
* Azure 备份会自动处理备份数据的存储。 默认情况下，保管库使用[异地冗余存储 (GRS)](../storage/common/storage-redundancy-grs.md)。 异地冗余可确保将备份数据复制到距主区域数百英里以外的辅助 Azure 区域。

按如下所述创建保管库：

1. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) 创建保管库。 指定要备份的 VM 的资源组名称和位置。

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. 许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。 出于此原因，在变量中存储备份恢复服务保管库对象可提供方便。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. 使用 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) 设置保管库上下文。

   * 保管库上下文是在保管库中受保护的数据的类型。
   * 设置上下文后，它将应用于所有后续 cmdlet

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>备份 Azure VM

备份会根据备份策略中指定的计划来运行。 在创建恢复服务保管库时，它附带了默认的保护和保留策略。

* 默认保护策略在一天的指定时间触发备份作业。
* 默认保留策略将每日恢复点保留 30 天。

为了在本教程中启用和备份 Azure VM，我们执行以下操作：

1. 使用 [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) 指定保管库中保存备份数据的容器。
2. 进行备份的每个 VM 都是一个项目。 若要启动备份作业，请使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) 获取有关 VM 的信息。
3. 使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) 运行按需备份。
    * 第一个初始备份作业会创建一个完整恢复点。
    * 初始备份之后，每个备份作业都会创建增量恢复点。
    * 增量恢复点有利于存储并具有时效性，因为它们仅传输自上次备份以来所做的更改。

启用并运行备份，如下所示：

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>故障排除

如果在备份虚拟机时遇到问题，请参阅此[故障排除文章](backup-azure-vms-troubleshoot.md)。

### <a name="deleting-a-recovery-services-vault"></a>删除恢复服务保管库

如果需要删除保管库，请首先删除保管库中的恢复点，然后将保管库取消注册，如下所示：

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>后续步骤

* [查看](backup-azure-vms-automation.md)使用 PowerShell 备份和还原 Azure VM 的更详细演练。
* [管理和监视 Azure VM](backup-azure-manage-vms.md)
* [还原 Azure VM](backup-azure-arm-restore-vms.md)
