---
title: 使用 PowerShell 部署和管理 Resource Manager 部署型 VM 的备份
description: 使用 PowerShell 在 Azure 中部署和管理 Resource Manager 部署型 VM 的备份
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa53a7e598b42e93e86eb059c36ff89f38bb7093
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300585"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>使用 PowerShell 备份和还原虚拟机

本文说明如何使用 Azure PowerShell cmdlet 从恢复服务保管库备份和恢复 Azure 虚拟机 (VM)。 恢复服务保管库是一种 Azure 资源管理器资源，用于保护 Azure 备份和 Azure Site Recovery 服务中的数据与资产。

> [!NOTE]
> Azure 有两种用于创建和使用资源的部署模型：[资源管理器部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文的内容与使用 Resource Manager 模型创建的 VM 有关。
>
>

本文将逐步指导使用 PowerShell 来保护 VM，以及从恢复点还原数据。

## <a name="concepts"></a>概念

如果不熟悉 Azure 备份服务，请参阅[什么是 Azure 备份？](backup-introduction-to-azure-backup.md)以大致了解该服务 在开始之前，请确保已掌握与 Azure 备份的必备组件相关的知识，并了解当前 VM 备份解决方案的限制。

若要有效地使用 PowerShell，必须了解对象的层次结构以及从何处开始。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

若要查看 AzureRm.RecoveryServices.Backup PowerShell cmdlet 参考，请参阅 Azure 库中的 [Azure Backup - Recovery Services Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup)（Azure 备份 - 恢复服务 Cmdlet）。

## <a name="setup-and-registration"></a>设置和注册

开始时，请执行以下操作：

1. [下载最新版本的 PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)（所需的最低版本为1.4.0）

2. 键入以下命令查找可用的 Azure 备份 PowerShell cmdlet：

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    将会显示 Azure 备份、Azure Site Recovery 和恢复服务保管库的别名和 cmdlet。 下图是你将看到的示例。 这不是 cmdlet 的完整列表。

    ![恢复服务列表](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. 使用 Connect-AzureRmAccount 登录到 Azure 帐户。 此 cmdlet 打开一个网页，提示输入帐户凭据：

    * 或者，还可使用 -Credential 参数将帐户凭据作为参数包含在 Connect-AzureRmAccount cmdlet 中。
    * 如果是代表租户的 CSP 合作伙伴，则需使用 tenantID 或租户主域名将客户指定为一名租户。 例如：Connect-AzureRmAccount -Tenant "fabrikam.com"

4. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起：

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. 如果是首次使用 Azure 备份，则必须使用 **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 可使用以下命令验证提供程序是否已成功注册：
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    在命令输出中，RegistrationState 应更改为 Registered。 如果未按此更改，只需重新运行 [Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider) cmdlet。

使用 PowerShell 可以自动化以下任务：

* [创建恢复服务保管库](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [备份 Azure VM](backup-azure-vms-automation.md#back-up-azure-vms)
* [触发备份作业](backup-azure-vms-automation.md#trigger-a-backup-job)
* [监视备份作业](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [还原 Azure VM](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

以下步骤引导创建恢复服务保管库。 恢复服务保管库不同于备份保管库。

1. 恢复服务保管库是一种 Resource Manager 资源，因此需要将它放在资源组中。 可以使用现有的资源组，也可以使用 **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. 使用 **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** cmdlet 创建恢复服务保管库。 确保为保管库指定的位置与用于资源组的位置是相同的。

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 指定要使用的存储冗余类型；可以使用[本地冗余存储 (LRS)](../storage/common/storage-redundancy-lrs.md) 或[异地冗余存储 (GRS)](../storage/common/storage-redundancy-grs.md)。 以下示例显示，testvault 的 -BackupStorageRedundancy 选项设置为 GeoRedundant。

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 许多 Azure 备份 cmdlet 要求使用恢复服务保管库对象作为输入。 出于此原因，在变量中存储备份恢复服务保管库对象可提供方便。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用 [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)：

```powershell
Get-AzureRmRecoveryServicesVault
```

输出与以下示例类似，请注意提供的关联 ResourceGroupName 和 Location（位置）。

```
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

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>创建保护策略

在创建恢复服务保管库时，它附带了默认的保护和保留策略。 默认保护策略在每天的指定时间触发备份作业。 默认保留策略将每日恢复点保留 30 天。 可以使用默认策略快速保护 VM，以后再使用不同的详细信息编辑该策略。

若要查看保管库中提供的保护策略，请使用 **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**。 可以使用此 cmdlet 获取特定策略，或者查看与某个工作负荷类型关联的策略。 以下示例获取适用于工作负荷类型 AzureVM 的策略。

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

输出类似于以下示例：

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell 中 BackupTime 字段的时区是 UTC。 但是，在 Azure 门户中显示备份时间时，该时间会根据本地时区调整。
>
>

一个备份保护策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留多长时间。 可以使用 **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** 查看默认保留策略。  类似地，可以使用 **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** 获取默认计划策略。 [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) cmdlet 创建用于保存备份策略信息的 PowerShell 对象。 计划和保留策略对象将用作 **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet 的输入。 以下示例将计划策略和保留策略存储在变量中。 此示例使用这些变量来定义在创建保护策略 *NewPolicy* 时要使用的参数。

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出类似于以下示例：

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>启用保护

定义保护策略后，仍然必须为项目启用策略。 可以使用 **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** 来启用保护。 启用保护需要两个对象 - 项和策略。 将策略与保管库关联之后，会在策略计划中定义的时间触发备份工作流。

以下示例使用策略 NewPolicy 启用对项目 V2VM 的保护。 示例会基于 VM 是否加密以及加密类型而有所不同。

在非加密的资源管理器 VM 上启用保护：

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

要在加密的 VM（使用 BEK 和 KEK 加密）上启用保护，必须为 Azure 备份服务授予从密钥保管库读取密钥和密码的权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

要在加密的 VM（仅使用 BEK 加密）上启用保护，必须为 Azure 备份服务授予从密钥保管库读取密码的权限。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> 如果在使用 Azure Government 云，请为 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet 中的参数 **-ServicePrincipalName** 使用值 ff281ffe-705c-4f53-9f37-a40e6f2c68f3。
>
>

在经典 VM 上启用保护：

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>修改保护策略

若要修改保护策略，请使用 [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) 修改 SchedulePolicy 或 RetentionPolicy 对象。

以下示例将恢复点保留期更改为 365 天。

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>触发备份

使用 [Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) 触发备份作业。 如果是初始备份，则其为完整备份。 后续备份将创建增量副本。 在触发备份作业之前，请确保使用 **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** 来设置保管库上下文。 以下示例假定已设置保管库上下文。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

输出类似于以下示例：

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell 中 StartTime 和 EndTime 字段的时区是 UTC。 但是，在 Azure 门户中显示时间时，该时间会根据本地时区调整。
>
>

## <a name="monitoring-a-backup-job"></a>监视备份作业

可以在不使用 Azure 门户的情况下监视长时间运行的操作，例如备份作业。 若要获取正在进行的作业的状态，请使用 **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet。 此 cmdlet 获取特定保管库的备份作业，并且该保管库是在保管库上下文中指定的。 以下示例将正在进行的作业的状态获取为数组，并将状态存储在 $joblist 变量中。

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

输出类似于以下示例：

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

与其使用额外的不必要的代码来轮询这些作业的完成情况，不如使用 **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet。 该 cmdlet 暂停操作的执行，直到作业完成或达到了指定的超时值。

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>还原 Azure VM

使用 Azure 门户还原 VM 与使用 PowerShell 还原 VM 存在很大区别。 如果使用 PowerShell，则从恢复点创建磁盘和配置信息时，还原操作即完成。 还原操作不会创建虚拟机。 若要通过磁盘创建虚拟机，请参阅[通过存储磁盘创建 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 部分。 如果不想还原整个 VM，而是要从 Azure VM 备份还原或恢复几个文件，请参阅[文件恢复部分](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)。

> [!Tip]
> 还原操作不会创建虚拟机。
>
>

下图显示了从 RecoveryServicesVault 到 BackupRecoveryPoint 的对象层次结构。

![显示 BackupContainer 的恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

若要还原备份数据，请确定已备份项目以及保留了时间点数据的恢复点。 使用 [Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem) 将数据从保管库还原到帐户。

还原 Azure VM 的基本步骤是：

* 选择 VM。
* 选择恢复点。
* 还原磁盘。
* 通过存储磁盘创建 VM。

### <a name="select-the-vm"></a>选择 VM

若要获取用于标识正确备份项的 PowerShell 对象，请从保管库中的容器开始，按对象层次结构进行操作。 要选择代表 VM 的容器，请使用 **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet，然后通过管道将其传递给 **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>选择恢复点

使用 **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet 列出备份项的所有恢复点。 然后选择要还原的恢复点。 如果不确定要使用哪个恢复点，则最好是选择列表中 RecoveryPointType = AppConsistent 的最新恢复点。

在以下脚本中，变量 **$rp** 是一个数组，其中包含所选备份项在过去七天的恢复点。 该数组按时间进行反向排序，以最新的恢复点作为索引 0。 使用标准 PowerShell 数组索引选取恢复点。 在示例中，$rp[0] 选择最新的恢复点。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

输出类似于以下示例：

```powershell
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

使用 **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet 将备份项的数据和配置还原到某个恢复点。 确定恢复点后，将其用作 -RecoveryPoint 参数的值。 在上述示例中，$rp[0] 是要使用的恢复点。 在下面的示例代码中，$rp[0] 是还原磁盘时要使用的恢复点。

还原磁盘和配置信息：

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>还原托管磁盘

> [!NOTE]
> 如果备份的 VM 具有托管磁盘，并且你想要将其还原为托管磁盘，我们从 Azure PowerShell RM 模块 v 6.7.0 引入了相关功能。 更高版本
>
>

提供了附加参数 TargetResourceGroupName 来指定托管磁盘要还原到的 RG。 

> [!NOTE]
> 强烈建议使用 **TargetResourceGroupName** 参数来还原托管磁盘，因为它可以显著提高性能。 此外，从 Azure Powershell Az 模块 1.0 开始，在使用托管磁盘进行还原时，此参数是必需的
>
>


```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

VMConfig.JSON 文件将还原到存储帐户，托管磁盘将还原到指定的目标 RG。

输出类似于以下示例：

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

使用 **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet 等待还原作业完成。

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

还原作业完成后，可以使用 **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** cmdlet 获取还原操作的详细信息。 JobDetails 属性提供重建 VM 所需的信息。

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

还原磁盘以后，转到下一部分来了解如何创建 VM。

## <a name="create-a-vm-from-restored-disks"></a>从还原的磁盘创建 VM

还原磁盘后，使用以下步骤通过磁盘创建和配置虚拟机。

> [!NOTE]
> 若要使用已还原的磁盘创建加密 VM，则 Azure 角色必须有权执行 **Microsoft.KeyVault/vaults/deploy/action** 操作。 如果角色不具有此权限，可通过此操作创建自定义角色。 有关详细信息，请参阅 [Custom Roles in Azure RBAC](../role-based-access-control/custom-roles.md)（Azure RBAC 中的自定义角色）。
>
>

> [!NOTE]
> 还原磁盘后，现在可以获得一个部署模板，你可以直接使用该模板创建新的 VM。 没有更多的不同的 PS cmdlet 来创建加密/非加密的托管/非托管 VM。

生成的作业详细信息提供可以查询和部署的模板 URI。

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

只需部署模板来创建新的 VM，如[此处](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source)所述。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

下一节列出使用“VMConfig”文件创建 VM 所需的步骤。

> [!NOTE]
> 强烈建议使用前面详细介绍的部署模板来创建 VM。 本部分（1-6 点）将在不久后弃用。

1. 查询已还原磁盘属性以获取作业详细信息。

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. 设置 Azure 存储上下文和还原 JSON 配置文件。

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. 使用 JSON 配置文件来创建 VM 配置。

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. 附加 OS 磁盘和数据磁盘。 此步骤提供多种托管和加密的 VM 配置的示例。 请使用适合 VM 配置的示例。

   * **非托管的非加密 VM** - 对于非托管的非加密 VM，请使用以下示例。

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **具有 Azure AD 的非托管加密 VM（仅限 BEK）**- 对于具有 Azure AD 的非托管加密 VM（仅使用 BEK 加密），需要先将密码还原到密钥保管库，然后才能附加磁盘。 有关详细信息，请参阅[从 Azure 备份恢复点还原加密的虚拟机](backup-azure-restore-key-secret.md)。 以下示例展示了如何为加密的 VM 附加 OS 和数据磁盘。 设置 OS 磁盘时，请确保提及相关的 OS 类型。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **具有 Azure AD 的非托管加密 VM（BEK 和 KEK）**- 对于具有 Azure AD 的非托管加密 VM（使用 BEK 和 KEK 加密），先将密钥和密码还原到密钥保管库，然后再附加磁盘。 有关详细信息，请参阅[从 Azure 备份恢复点还原加密的虚拟机](backup-azure-restore-key-secret.md)。 以下示例展示了如何为加密的 VM 附加 OS 和数据磁盘。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **不具有 Azure AD 的非托管加密 VM（仅限 BEK）**- 对于不具有 Azure AD 的非托管加密 VM（仅使用 BEK 加密），如果源 keyVault/密码不可用，可使用[从 Azure 备份恢复点还原非加密虚拟机](backup-azure-restore-key-secret.md)中的过程，将密码还原到密钥保管库。 然后执行以下脚本在已还原的 OS Blob 上设置加密详细信息（此步骤不需要数据 Blob）。 可以从已还原的 keyVault 提取 $dekurl。<br>

   仅当源 keyVault/密码不可用时，才需要执行下面的脚本。

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    密码可用且在 OS Blob 上设置加密详细信息后，请使用下面给出的脚本附加磁盘。<br>

    如果源 keyVault/密码已经可用，则无需执行以上脚本。

      ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **不具有 Azure AD 的非托管加密 VM（BEK和 KEK）**- 对于不具有 Azure AD 的非托管加密 VM（使用 BEK 和 KEK 加密），如果源 keyVault/密钥/密码不可用，可使用[从 Azure 备份恢复点还原非加密虚拟机](backup-azure-restore-key-secret.md)中的过程，将密钥和密码还原到密钥保管库。 然后执行以下脚本在已还原的 OS Blob 上设置加密详细信息（此步骤不需要数据 Blob）。 可以从已还原的 keyVault 提取 $dekurl 和 $kekurl。

   仅当源 keyVault/密钥/密码不可用时，才需要执行下面的脚本。

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzureStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   密钥/密码可用且在 OS Blob 上设置加密详细信息后，请使用下面给出的脚本附加磁盘。

    如果源 keyVault/密钥/密码可用，则无需执行以上脚本。

    ```powershell
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

  * 托管的非加密 VM -对于托管的非加密 VM，将附加还原的托管磁盘。 有关详细信息，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

  * **具有 Azure AD 的托管加密 VM（仅限 BEK）**- 对于具有 Azure AD 的托管加密 VM（仅使用 BEK 加密），将附加还原的托管磁盘。 有关详细信息，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

  * **具有 Azure AD 的托管加密 VM（BEK 和 KEK）**- 对于具有 Azure AD 的托管加密 VM（使用 BEK 和 KEK 加密），将附加还原的托管磁盘。 有关详细信息，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

  * **不具有 Azure AD 的托管加密 VM（仅限 BEK）**- 对于不具有 Azure AD 的托管加密 VM（仅使用 BEK 加密），如果源 keyVault/密码不可用，可使用[从 Azure 备份恢复点还原非加密虚拟机](backup-azure-restore-key-secret.md)中的过程，将密码还原到密钥保管库。然后执行以下脚本在已还原的 OS 磁盘上设置加密详细信息（此步骤不需要数据磁盘）。 可以从已还原的 keyVault 提取 $dekurl。

    仅当源 keyVault/密码不可用时，才需要执行下面的脚本。  

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

    密码可用且在 OS 磁盘上设置加密详细信息后，若要附加还原的托管磁盘，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

  * **不具有 Azure AD 的托管加密 VM（BEK和 KEK）**- 对于不具有 Azure AD 的托管加密 VM（使用 BEK 和 KEK 加密），如果源 keyVault/密钥/密码不可用，可使用[从 Azure 备份恢复点还原非加密虚拟机](backup-azure-restore-key-secret.md)中的过程，将密钥和密码还原到密钥保管库。 然后执行以下脚本在已还原的 OS 磁盘上设置加密详细信息（此步骤不需要数据磁盘）。 可以从已还原的 keyVault 提取 $dekurl 和 $kekurl。

  仅当源 keyVault/密钥/密码不可用时，才需要执行下面的脚本。

  ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzureRmDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzureRmDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzureRmDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    密钥/密码可用且在 OS 磁盘上设置加密详细信息后，若要附加还原的托管磁盘，请参阅[使用 PowerShell 将数据磁盘附加到 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

5. 设置网络设置。

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. 创建虚拟机。

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. 推送 ADE 扩展。

  * **对于具有 Azure AD 的 VM** - 使用以下命令手动对数据磁盘启用加密  

    **仅限 BEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

    **BEK 和 KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

  * **对于不具有 Azure AD 的 VM** - 使用以下命令手动对数据磁盘启用加密。

    如果在命令执行期间出现请求 AADClientID，则需要更新 Azure PowerShell。

    **仅限 BEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK 和 KEK**

      ```powershell  
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>从 Azure VM 备份还原文件

除了可以还原磁盘以外，还可以从 Azure VM 备份还原单个文件。 还原文件功能提供对恢复点中所有文件的访问权限。 通过文件资源管理器管理文件，与普通文件的管理方法相同。

从 Azure VM 备份还原文件的基本步骤如下：

* 选择 VM
* 选择恢复点
* 装载恢复点的磁盘
* 复制所需的文件
* 卸载磁盘

### <a name="select-the-vm"></a>选择 VM

若要获取用于标识正确备份项的 PowerShell 对象，请从保管库中的容器开始，按对象层次结构进行操作。 要选择代表 VM 的容器，请使用 **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet，然后通过管道将其传递给 **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet。

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>选择恢复点

使用 **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet 列出备份项的所有恢复点。 然后选择要还原的恢复点。 如果不确定要使用哪个恢复点，则最好是选择列表中 RecoveryPointType = AppConsistent 的最新恢复点。

在以下脚本中，变量 **$rp** 是一个数组，其中包含所选备份项在过去七天的恢复点。 该数组按时间进行反向排序，以最新的恢复点作为索引 0。 使用标准 PowerShell 数组索引选取恢复点。 在示例中，$rp[0] 选择最新的恢复点。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

输出类似于以下示例：

```
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
> 这些磁盘作为 iSCSI 附加磁盘装载到运行此脚本的计算机中。 装载将立刻开始，并且不会产生任何费用。
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

输出类似于以下示例：

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

在要在它上面恢复文件的计算机上运行此脚本。 要执行该脚本，必须输入提供的密码。 附加磁盘后，使用 Windows 文件资源管理器浏览新的卷和文件。 有关详细信息，请参阅备份文章[从 Azure 虚拟机备份恢复文件](backup-azure-restore-files-from-vm.md)。

### <a name="unmount-the-disks"></a>卸载磁盘

复制所需文件后，请使用 **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** 卸载磁盘。 请务必卸载磁盘，以便删除对恢复点文件的访问权限。

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>后续步骤

如果更愿意使用 PowerShell 来处理 Azure 资源，请查看 PowerShell 文章：[为 Windows Server 部署和管理备份](backup-client-automation.md)。 如果管理 DPM 备份，请参阅[为 DPM 部署和管理备份](backup-dpm-automation.md)。 这两篇文章都为 Resource Manager 部署和经典部署提供了一个版本。  
