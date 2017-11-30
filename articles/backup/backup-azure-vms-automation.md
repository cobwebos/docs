---
title: "使用 PowerShell 部署和管理 Resource Manager 部署型 VM 的备份 | Microsoft Docs"
description: "使用 PowerShell 在 Azure 中部署和管理 Resource Manager 部署型 VM 的备份"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddd45dfb1f9e08add7a61a42e4f9b570dc25495d
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>使用 AzureRM.RecoveryServices.Backup cmdlet 来备份虚拟机
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [经典](backup-azure-vms-classic-automation.md)
>
>

本文说明如何使用 Azure PowerShell cmdlet 从恢复服务保管库备份和恢复 Azure 虚拟机 (VM)。 恢复服务保管库是一种 Azure 资源管理器资源，用于保护 Azure 备份和 Azure Site Recovery 服务中的数据与资产。 可以使用恢复服务保管库来保护 Azure Service Manager 部署型 VM 以及 Azure 资源管理器部署型 VM。

> [!NOTE]
> Azure 有两种用于创建和使用资源的部署模型： [Resource Manager 部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文的内容与使用 Resource Manager 模型创建的 VM 有关。
>
>

本文将逐步指导使用 PowerShell 来保护 VM，以及从恢复点还原数据。

## <a name="concepts"></a>概念
如果不熟悉 Azure 备份服务，请查看[什么是 Azure 备份？](backup-introduction-to-azure-backup.md)，即可大致了解该服务。 在开始之前，请确保已掌握与系统必备组件相关的基础知识（这些必备组件是使用 Azure 备份所必需的），并了解当前 VM 备份解决方案的限制。

若要有效地使用 PowerShell，必须了解对象的层次结构以及从何处开始。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

若要查看 AzureRm.RecoveryServices.Backup PowerShell cmdlet 参考，请参阅 Azure 库中的 [Azure Backup - Recovery Services Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)（Azure 备份 - 恢复服务 Cmdlet）。

## <a name="setup-and-registration"></a>设置和注册
开始时，请执行以下操作：

1. [下载最新版本的 PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)（所需的最低版本为 1.4.0）
2. 键入以下命令查找可用的 Azure 备份 PowerShell cmdlet：

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


使用 PowerShell 可以自动化以下任务：

* 创建恢复服务保管库
* 备份 Azure VM
* 触发备份作业
* 监视备份作业
* 还原 Azure VM

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
以下步骤引导创建恢复服务保管库。 恢复服务保管库不同于备份保管库。

1. 如果是首次使用 Azure 备份，则必须使用 **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. 恢复服务保管库是一种 Resource Manager 资源，因此需要将它放在资源组中。 可以使用现有的资源组，也可以使用 **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。  

    ```
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
3. 使用 **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** cmdlet 创建恢复服务保管库。 确保为保管库指定的位置与用于资源组的位置是相同的。

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. 指定要使用的存储冗余类型；可以使用[本地冗余存储 (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) 或[异地冗余存储 (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)。 以下示例显示，testvault 的 -BackupStorageRedundancy 选项设置为 GeoRedundant。

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。 出于此原因，在变量中存储备份恢复服务保管库对象可提供方便。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库
使用 **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** 查看当前订阅中所有保管库的列表。 可以使用此命令来查看是否创建了新的保管库，或者查看订阅中的可用保管库。

运行 Get-AzureRmRecoveryServicesVault 命令可查看订阅中的所有保管库。 以下示例展示了为每个保管库显示的信息。

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>备份 Azure VM
使用恢复服务保管库保护虚拟机。 应用保护前，请先设置保管库上下文（保管库中受保护的数据类型）并验证保护策略。 保护策略是指备份作业运行时以及每个备份快照的保留时长的计划。

### <a name="set-vault-context"></a>设置保管库上下文
在 VM 上启用保护之前，请使用 **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** 来设置保管库上下文。 设置保管库上下文后，它将应用于所有后续 cmdlet。 以下示例为保管库 *testvault* 设置保管库上下文。

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>创建保护策略
在创建恢复服务保管库时，它附带了默认的保护和保留策略。 默认保护策略在每天的指定时间触发备份作业。 默认保留策略将每日恢复点保留 30 天。 可以使用默认策略快速保护 VM，以后再使用不同的详细信息编辑该策略。

若要查看保管库中的保护策略，请使用 **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**。 可以使用此 cmdlet 获取特定策略，或者查看与某个工作负荷类型关联的策略。 以下示例获取适用于工作负荷类型 AzureVM 的策略。

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell 中 BackupTime 字段的时区是 UTC。 但是，在 Azure 门户中显示备份时间时，该时间会根据本地时区调整。
>
>

一个备份保护策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留多长时间。 可以使用 **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** 查看默认保留策略。  类似地，可以使用 **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** 获取默认计划策略。 [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) cmdlet 创建用于保存备份策略信息的 PowerShell 对象。 计划和保留策略对象将用作 **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet 的输入。 以下示例将计划策略和保留策略存储在变量中。 此示例使用这些变量来定义在创建保护策略 *NewPolicy* 时要使用的参数。

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>启用保护
在定义备份保护策略后，还必须为相应的项启用该策略。 可以使用 **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** 来启用保护。 启用保护需要两个对象 - 项和策略。 将策略与保管库关联之后，会在策略计划中定义的时间触发备份工作流。

以下示例使用策略 NewPolicy 为项 V2VM 启用保护。 在未加密型 Resource Manager VM 上启用保护

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

若要在加密型 VM 上启用保护（使用 BEK 和 KEK 加密），需要向 Azure 备份服务授予权限来读取密钥保管库中的密钥和密码。

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

若要在加密型 VM（仅使用 BEK 加密）上启用保护，需要向 Azure 备份服务授予权限来读取密钥保管库中的密码。

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> 如果在使用 Azure Government 云，请为 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet 中的参数 **-ServicePrincipalName** 使用值 ff281ffe-705c-4f53-9f37-a40e6f2c68f3。
>
>

对于经典 VM

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>修改保护策略
若要修改保护策略，请使用 [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) 修改 SchedulePolicy 或 RetentionPolicy 对象。

以下示例将恢复点保留期更改为 365 天。

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>触发备份
可以使用 **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** 来触发备份作业。 如果它是初始备份，则是一个完整备份。 后续备份将创建增量副本。 在触发备份作业之前，请确保使用 **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** 来设置保管库上下文。 以下示例假定已设置了保管库上下文。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell 中 StartTime 和 EndTime 字段的时区是 UTC。 但是，在 Azure 门户中显示时间时，该时间会根据本地时区调整。
>
>

## <a name="monitoring-a-backup-job"></a>监视备份作业
可以在不使用 Azure 门户的情况下监视长时间运行的操作，例如备份作业。 若要获取正在进行的作业的状态，请使用 **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet。 此 cmdlet 获取特定保管库的备份作业，并且该保管库是在保管库上下文中指定的。 以下示例将正在进行的作业的状态获取为数组，并将状态存储在 $joblist 变量中。

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

与其使用额外的不必要的代码来轮询这些作业的完成情况，不如使用 **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet。 该 cmdlet 暂停操作的执行，直到作业完成或达到了指定的超时值。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>还原 Azure VM
使用 Azure 门户还原 VM 与使用 PowerShell 还原 VM 存在很大区别。 如果使用 PowerShell，则从恢复点创建磁盘和配置信息时，还原操作即完成。 若要从 Azure VM 备份还原或恢复几个文件，请参阅[文件恢复部分](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> 还原操作不会创建虚拟机。
>
>

若要通过磁盘创建虚拟机，请参阅[通过存储磁盘创建 VM](backup-azure-vms-automation.md#create-a-vm-from-stored-disks)。 还原 Azure VM 的基本步骤是：

* 选择 VM
* 选择恢复点
* 还原磁盘
* 通过存储磁盘创建 VM

下图显示了从 RecoveryServicesVault 到 BackupRecoveryPoint 的对象层次结构。

![显示 BackupContainer 的恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

若要还原备份数据，请确定已备份项目以及保留了时间点数据的恢复点。 使用 **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet 将数据从保管库还原到客户的帐户。

### <a name="select-the-vm"></a>选择 VM
若要获取用于标识正确备份项的 PowerShell 对象，请从保管库中的容器开始，按对象层次结构进行操作。 要选择代表 VM 的容器，请使用 **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet，然后通过管道将其传递给 **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>选择恢复点
使用 **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet 列出备份项的所有恢复点。 然后选择要还原的恢复点。 如果不确定要使用哪个恢复点，则最好是选择列表中 RecoveryPointType = AppConsistent 的最新恢复点。

在以下脚本中，变量 **$rp** 是一个数组，其中包含所选备份项在过去七天的恢复点。 该数组按时间进行反向排序，以最新的恢复点作为索引 0。 使用标准 PowerShell 数组索引选取恢复点。 在示例中，$rp[0] 选择最新的恢复点。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>还原磁盘
使用 **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet 将备份项的数据和配置还原到某个恢复点。 确定某个恢复点后，即可使用它作为 -RecoveryPoint 参数的值。 在上面的示例代码中，$rp[0] 是要使用的恢复点。 在下面的示例代码中，$rp[0] 是还原磁盘时要使用的恢复点。

还原磁盘和配置信息：

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

使用 **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet 等待还原作业完成。

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

还原作业完成后，可以使用 **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** cmdlet 获取还原操作的详细信息。 JobDetails 属性提供重建 VM 所需的信息。

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

还原磁盘以后，转到下一部分来了解如何创建 VM。

## <a name="create-a-vm-from-restored-disks"></a>从还原的磁盘创建 VM
还原磁盘以后，即可通过以下步骤从磁盘创建和配置虚拟机。

> [!NOTE]
> 若要使用已还原的磁盘创建加密 VM，则 Azure 角色必须有权执行 **Microsoft.KeyVault/vaults/deploy/action** 操作。 如果角色不具有此权限，可通过此操作创建自定义角色。 有关详细信息，请参阅 [Custom Roles in Azure RBAC](../active-directory/role-based-access-control-custom-roles.md)（Azure RBAC 中的自定义角色）。
>
>

1. 查询已还原磁盘属性以获取作业详细信息。

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. 设置 Azure 存储上下文和还原 JSON 配置文件。

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. 使用 JSON 配置文件来创建 VM 配置。

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. 附加 OS 磁盘和数据磁盘。 请根据 VM 配置，参阅相关部分内容，查看各自的 cmdlet：

    #### <a name="non-managed-non-encrypted-vms"></a>非托管、非加密型 VM

    对于非托管、非加密型 VM，请使用以下示例。

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>非托管、加密型 VM（仅限 BEK）

    对于非托管的加密 VM（仅使用 BEK 加密），需要先将密码还原到密钥保管库，然后才能附加磁盘。 有关详细信息，请参阅[从 Azure 备份恢复点还原加密虚拟机](backup-azure-restore-key-secret.md)一文。 以下示例展示了如何为加密的 VM 附加 OS 和数据磁盘。

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>非托管、加密型 VM（BEK 和 KEK）

    对于非托管的加密 VM（使用 BEK 和 KEK 加密），需要先将密钥和密码还原到密钥保管库，然后才能附加磁盘。 有关详细信息，请参阅[从 Azure 备份恢复点还原加密虚拟机](backup-azure-restore-key-secret.md)一文。 以下示例展示了如何为加密的 VM 附加 OS 和数据磁盘。

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>托管、非加密型 VM

    对于托管非加密型 VM，需从 blob 存储创建托管磁盘，然后附加磁盘。 有关详细信息，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md) 一文。 以下示例代码展示了如何为托管非加密型 VM 附加数据磁盘。

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>托管加密型 VM（仅限 BEK）

    对于托管加密型 VM（仅使用 BEK 加密），需要从 Blob 存储创建托管磁盘，然后附加磁盘。 有关详细信息，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md) 一文。 以下示例代码展示了如何为托管加密 VM 附加数据磁盘。

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>托管加密型 VM（BEK 和 KEK）

    对于托管加密 VM（使用 BEK 和 KEK 加密），需要从 Blob 存储创建托管磁盘，然后附加磁盘。 有关详细信息，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md) 一文。 以下示例代码展示了如何为托管加密 VM 附加数据磁盘。

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. 设置网络设置。

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. 创建虚拟机。

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>从 Azure VM 备份还原文件

除了可以还原磁盘以外，还可以从 Azure VM 备份还原单个文件。 可以使用还原文件功能访问某个恢复点的所有文件，并可以像管理正常文件一样通过文件资源管理器管理这些文件。

从 Azure VM 备份还原文件的基本步骤是：

* 选择 VM
* 选择恢复点
* 装载恢复点的磁盘
* 复制所需的文件
* 卸载磁盘


### <a name="select-the-vm"></a>选择 VM
若要获取用于标识正确备份项的 PowerShell 对象，请从保管库中的容器开始，按对象层次结构进行操作。 要选择代表 VM 的容器，请使用 **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet，然后通过管道将其传递给 **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>选择恢复点
使用 **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet 列出备份项的所有恢复点。 然后选择要还原的恢复点。 如果不确定要使用哪个恢复点，则最好是选择列表中 RecoveryPointType = AppConsistent 的最新恢复点。

在以下脚本中，变量 **$rp** 是一个数组，其中包含所选备份项在过去七天的恢复点。 该数组按时间进行反向排序，以最新的恢复点作为索引 0。 使用标准 PowerShell 数组索引选取恢复点。 在示例中，$rp[0] 选择最新的恢复点。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>装载恢复点的磁盘

使用 [Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript) cmdlet 获取用于装载恢复点的所有磁盘的脚本。

> [!NOTE]
> 这些磁盘作为 iSCSI 附加磁盘装载到运行此脚本的计算机中。 因此，此过程几乎可在瞬间完成，并且不会产生任何费用。
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
在要在它上面恢复文件的计算机上运行此脚本。 需要输入上述密码才能执行此脚本。 附加磁盘后，使用 Windows 文件资源管理器浏览新的卷和文件。 有关详细信息，请参阅[文件恢复文档](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>卸载磁盘
复制所需的文件后，使用 [Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0) cmdlet 卸载磁盘。 强烈建议使用上述步骤，因为它可确保删除对恢复点的文件的访问权限

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>后续步骤
如果更愿意使用 PowerShell 来处理 Azure 资源，请查看 PowerShell 文章：[为 Windows Server 部署和管理备份](backup-client-automation.md)。 如果管理 DPM 备份，请参阅[为 DPM 部署和管理备份](backup-dpm-automation.md)。 这两篇文章都为 Resource Manager 部署和经典部署提供了一个版本。  
