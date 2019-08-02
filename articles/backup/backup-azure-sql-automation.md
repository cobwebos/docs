---
title: Azure 备份：使用 Azure 备份和 PowerShell 备份和还原 Azure Vm 中的 SQL 数据库
description: 使用 Azure 备份和 PowerShell 备份和还原 Azure Vm 中的 SQL 数据库。
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Azure 备份;TRANSACT-SQL
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: dacurwin
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: b31043ad445f0ac1459772fe630358fc38eaa9c2
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689062"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>在 Azure Vm 中通过 PowerShell 备份和还原 SQL 数据库

本文介绍如何使用 Azure PowerShell 在 Azure VM 中使用[Azure 备份](backup-overview.md)恢复服务保管库备份和恢复 SQL 数据库。

本教程介绍了如何完成以下操作：

> [!div class="checklist"]
> * 设置 PowerShell 并注册 Azure 恢复服务提供程序。
> * 创建恢复服务保管库。
> * 在 Azure VM 中配置 SQL 数据库的备份。
> * 运行备份作业。
> * 还原已备份的 SQL 数据库。
> * 监视备份和还原作业。

## <a name="before-you-start"></a>开始之前

* [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
* 阅读有关在[Azure vm 中备份 SQL](backup-azure-sql-database.md#before-you-start)数据库的功能功能。
* 查看恢复服务的 PowerShell 对象层次结构。

### <a name="recovery-services-object-hierarchy"></a>恢复服务对象层次结构

下图汇总了对象层次结构。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 库中的 **Az.RecoveryServices** [cmdlet 参考](/powershell/module/az.recoveryservices)。

### <a name="set-up-and-install"></a>设置和安装

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

按如下所述设置 PowerShell：

1. [下载最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)。 所需的最低版本为1.5.0。

2. 找到包含以下命令的 Azure 备份 PowerShell cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 备份和恢复服务保管库的别名和 cmdlet。 下面是可能显示的内容示例。 此 cmdlet 列表并不完整。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用 **Connect-AzAccount** 登录到 Azure 帐户。
5. 在出现的网页上，系统会提示你输入帐户凭据。

    * 或者，可以结合 **-Credential** 在 **Connect-AzAccount** cmdlet 中将帐户凭据包含为参数。
    * 如果你是使用租户的 CSP 合作伙伴, 请使用其 tenantID 或租户主域名将客户指定为租户。 例如，Connect-AzAccount -Tenant fabrikam.com。

6. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 首次使用 Azure 备份时，请使用 Register-AzResourceProvider cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 验证提供程序是否已成功注册：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令输出中，确认 **RegistrationState** 是否更改为 **Registered**。 如果不是，请再次运行 **Register-AzResourceProvider** cmdlet。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

请按照以下步骤创建恢复服务保管库。

恢复服务保管库是一种资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用 New-AzResourceGroup cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。

1. 保管库放在资源组中。 如果没有现有的资源组，请使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) 新建一个。 此示例在美国西部区域创建一个新的资源组。

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 使用 [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 创建保管库。 请为保管库指定与资源组相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定保管库存储使用的冗余类型。

    * 可以使用[本地冗余存储](../storage/common/storage-redundancy-lrs.md)或[异地冗余存储](../storage/common/storage-redundancy-grs.md)。
    * 下面的示例将**BackupStorageRedundancy**选项设置为[AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd For **testvault**设置为**GeoRedundant**。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

输出如下所示。 已提供关联的资源组和位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>设置保管库上下文

将保管库对象存储在变量中，并设置保管库上下文。

* 许多 Azure 备份 cmdlet 要求将恢复服务保管库对象用作输入，因此可以方便地在变量中存储保管库对象。
* 保管库上下文是在保管库中受保护的数据的类型。 可以使用 [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) 设置它。 设置上下文后，它将应用于所有后续 cmdlet。

以下示例为 testvault 设置保管库上下文。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保管库 ID

我们已计划根据 Azure PowerShell 指导原则弃用保管库上下文设置。 你可以改为存储或提取保管库 ID，并将其传递给相关命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略指定备份计划，以及备份恢复点的保留期限：

* 一个备份策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。
* 使用 [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0) 查看默认的备份保留策略。
* 使用 [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0) 查看默认的备份策略计划。
* 使用 [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet 创建新的备份策略。 输入计划和保留策略对象。

以下示例将计划策略和保留策略存储在变量中。 然后, 它使用这些变量作为新策略 (**NewSQLPolicy**) 的参数。 **NewSQLPolicy**采用每日一次的 "完整" 备份, 保留180天, 并每隔2小时进行一次日志备份

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>启用备份

### <a name="registering-the-sql-vm"></a>注册 SQL VM

对于 Azure VM 备份和 Azure 文件共享, 备份服务可以连接到这些 Azure 资源管理器资源并提取相关的详细信息。 由于 SQL 是 Azure VM 内的应用程序, 因此备份服务需要访问应用程序和提取必要详细信息的权限。 要执行此操作, 需要使用恢复服务保管库 *"注册"* 包含 SQL 应用程序的 Azure VM。 一旦向保管库注册了 SQL VM, 就可以仅保护该保管库的 SQL 数据库。 使用[AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS CMDLET 注册 VM。

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

此命令将返回此资源的 "备份容器", 状态将为 "已注册"

> [!NOTE]
> 如果未指定 force 参数, 则要求用户确认文本 "是否要禁用对此容器的保护"。 请忽略此文本, 并说 "Y" 以确认。 这是一个已知问题, 我们正在努力删除文本和强制参数的要求

### <a name="fetching-sql-dbs"></a>获取 SQL 数据库

完成注册后, 备份服务将能够列出 VM 内所有可用的 SQL 组件。 若要查看尚未备份到此保管库的所有 SQL 组件, 请使用[AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

此输出将显示所有通过项类型和 ServerName 注册到此保管库的 SQL Vm 中所有未受保护的 SQL 组件。 通过传递 "-Container" 参数或使用 "Name" 和 "ServerName" 的组合以及 ItemType 标志来实现唯一的 SQL 项目, 可以进一步筛选到特定的 SQL VM。

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>配置备份

现在, 我们有了所需的 SQL DB 和需要备份的策略, 可以使用[AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet 来配置此 sql DB 的备份。

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

该命令将一直等到配置备份完成并返回以下输出。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>正在提取新的 SQL 数据库

注册计算机后, 备份服务会提取可用数据库的详细信息。 如果用户稍后向注册的计算机添加 SQL 数据库/SQL 实例, 则需要手动触发备份服务以执行全新的 "查询", 以再次获取所有不受保护的数据库 (包括新添加的数据库)。 在 SQL VM 上使用[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 来执行全新查询。 命令将等待, 直到操作完成。 稍后使用[AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 获取未受保护的最新 SQL 组件列表

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

提取相关的可保护项后, 按[上述部分](#configuring-backup)所述启用备份。
如果不想手动检测新数据库, 则可以选择 start-autoprotection, 如下所[述。](#enable-autoprotection)

## <a name="enable-autoprotection"></a>启用 Start-autoprotection

用户可以配置备份, 以便将来添加的所有数据库都将使用特定策略自动保护。 若要启用 start-autoprotection, 请使用[AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet。

由于此说明是备份所有未来的数据库, 因此该操作在 SQLInstance 级别进行。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

给定 start-autoprotection 意向后, 对计算机进行的查询以提取新添加的数据库时, 每隔8小时就会发生一次计划的后台任务。

## <a name="restore-sql-dbs"></a>还原 SQL 数据库

Azure 备份可以还原在 Azure Vm 上运行 SQL Server 数据库, 如下所示:

1. 使用事务日志备份还原到特定的日期或时间 (到第二个)。 Azure 备份会自动根据所选时间确定所需的完整差异备份和日志备份链。
2. 还原特定的完整备份或差异备份以还原到特定恢复点。

在还原 SQL 数据库之前, 请检查[此处](restore-sql-database-azure-vm.md#prerequisites)提到的先决条件。

首先, 使用[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 提取相关的已备份 SQL DB。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>提取相关的还原时间

如上所述, 用户可以将备份的 SQL 数据库还原到完整/差异副本**或**日志时间点。

#### <a name="fetch-distinct-recovery-points"></a>提取不同的恢复点

使用[AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0)为备份的 SQL 数据库提取不同 (完全/差异) 的恢复点。

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

输出类似于下面的示例

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

使用 "RecoveryPointId" 筛选器或数组筛选器提取相关的恢复点。

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>提取时点恢复点

如果用户想要将数据库还原到某个时间点, 请使用[AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet。 该 cmdlet 将返回一个日期列表, 该列表表示该 SQL 备份项目的连续连续日志链的开始时间和结束时间。 所需的时间点应在此范围内。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

输出将与下面的示例类似。

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

上述输出意味着用户可以还原到所显示开始时间和结束时间之间的任何时间点。 时间采用 UTC 格式。 在 PS 中构造位于上述范围内的任何时间点。

> [!NOTE]
> 选择还原的日志时间点时, 用户不需要指定从其还原数据库的起始点, 即完整备份。 Azure 备份服务将负责整个恢复计划, 即要选择哪一完整备份, 以及要应用的日志备份等。

### <a name="determine-recovery-configuration"></a>确定恢复配置

对于 SQL 数据库还原, 支持以下还原方案。

1. 用其他恢复点中的数据替代备份的 SQL DB-OriginalWorkloadRestore
2. 在同一 SQL 实例中将 SQL 数据库还原为新数据库-AlternateWorkloadRestore
3. 在另一个 SQL 实例中将 SQL 数据库作为新数据库还原到另一个 SQL 实例-AlternateWorkloadRestore

在提取相关恢复点 (不同或日志时间点) 后, 使用[AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 按照所需的恢复计划提取恢复配置对象。

#### <a name="original-workload-restore"></a>原始工作负荷还原

若要用恢复点中的数据替代备份的数据库, 只需指定右标志和相关恢复点, 如以下示例中所示。

##### <a name="original-restore-with-distinct-recovery-point"></a>具有不同恢复点的原始还原

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>具有日志时间点的原始还原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>备用工作负荷还原

> [!IMPORTANT]
> 只有在 Azure VM "注册" 到此保管库的情况下, 才能将备份的 SQL 数据库作为新数据库还原到另一个 SQLInstance。

如上所述, 如果目标 SQLInstance 位于另一个 Azure VM 中, 请确保将其[注册到此保管库](#registering-the-sql-vm), 并且相关的 SQLInstance 显示为可保护的项。

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

然后, 只需传递相关恢复点, 将 SQL 实例定位到正确的标志, 如下所示。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>用不同的恢复点进行备用还原

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>具有日志时间点的备用还原

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

从[AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 获取的最终恢复点配置对象具有用于还原的所有相关信息, 如下所示。

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

可以编辑已还原的数据库名称、OverwriteWLIfpresent、NoRecoveryMode 和 targetPhysicalPath 字段。 获取目标文件路径的更多详细信息, 如下所示。

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

将相关 PS 属性设置为字符串值, 如下所示。

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> 请确保最终恢复配置对象具有所有必需的和正确的值, 因为还原操作将基于 config 对象。

### <a name="restore-with-relevant-configuration"></a>还原相关配置

获取并验证相关恢复配置对象后, 使用[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 来启动还原过程。

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

还原操作返回要跟踪的作业。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>管理 SQL 备份

### <a name="on-demand-backup"></a>按需备份

为 DB 启用备份后, 用户还可以使用[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS CMDLET 为数据库触发按需备份。 下面的示例在启用了压缩功能的情况下, 在 SQL 数据库上触发完整备份, 并且完整备份应保留60天。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

即席备份命令返回要跟踪的作业。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

如果输出丢失或要获取相关的作业 ID, 请从 Azure 备份服务[获取作业列表](#track-azure-backup-jobs), 然后跟踪其详细信息。

### <a name="change-policy-for-backup-items"></a>更改备份项的策略

用户可以修改现有策略，也可以将备份项的策略从 Policy1 更改为 Policy2。 若要切换备份项的策略，只需提取相关策略并备份项，并使用 [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) 命令以备份项作为参数。

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

该命令将一直等到配置备份完成并返回以下输出。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>重新注册 SQL Vm

> [!WARNING]
> 在尝试重新注册之前, 请务必阅读本[文档](backup-sql-server-azure-troubleshoot.md#re-registration-failures)以了解失败的症状和原因

若要触发 SQL VM 的重新注册, 请提取相关的备份容器, 并将其传递给 register cmdlet。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>停止保护

#### <a name="retain-data"></a>保留数据

如果用户想要停止保护，他们可以使用 [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet。 此命令将停止计划的备份，但到目前为止备份的数据将永远保留。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>删除备份数据

若要完全删除保管库中存储的备份数据，只需将“-RemoveRecoveryPoints”标志/开关添加到[“disable”保护命令](#retain-data)。

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>禁用自动保护

如果 start-autoprotection 是在 SQLInstance 上配置的, 则用户可以使用[AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet 来禁用它。

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>注销 SQL VM

如果 SQL server 的所有数据库[都不再受保护, 并且不存在任何备份数据](#delete-backup-data), 则用户可以从此保管库中取消注册 sql VM。 只有这样, 用户才可以保护 Db 到另一个保管库。 使用[AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS CMDLET 注销 SQL VM。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>跟踪 Azure 备份作业

请务必注意, Azure 备份仅在 SQL 备份中跟踪用户触发的作业。 计划的备份 (包括日志备份) 在门户/powershell 中不可见。 但是, 如果任何计划的作业失败, 将生成一个[备份警报](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)并将其显示在门户中。 [使用 Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)跟踪所有计划作业和其他相关信息。

用户可以跟踪即席/用户触发的操作, 其中包含在异步作业 (如 backup) 的[输出](#on-demand-backup)中返回的 JobID。 使用[AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet 跟踪作业及其详细信息。

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

若要从 Azure 备份服务获取即席作业及其状态的列表, 请使用[AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet。 下面的示例返回所有正在进行的 SQL 作业。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

若要取消正在进行的作业, 请使用[AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet。

## <a name="managing-sql-always-on-availability-groups"></a>管理 SQL Always On 可用性组

对于 SQL Always On 可用性组, 请确保注册可用性组 (AG) 的[所有节点](#registering-the-sql-vm)。 对所有节点执行注册后, 会在 "可保护项" 下以逻辑方式创建 SQL 可用性组对象。 SQL AG 下的数据库将列为 "Backup-sqldatabase"。 节点将显示为独立的实例, 并且它们下面的默认 SQL 数据库也将作为 SQL 数据库列出。

例如, 假设 SQL AG 有两个节点: "sql-server-0" 和 "sql-server-1" 和1个 SQL AG DB。 一旦注册了这两个节点, 如果用户列出了可保护[的项](#fetching-sql-dbs), 它将列出以下组件

1. 作为 New-sqlavailabilitygroup 的 SQL AG 对象可保护项类型
2. 作为 Backup-sqldatabase 的 SQL AG DB 可保护项类型
3. sql-服务器 0-可保护的项类型为 SQLInstance
4. sql-server 1-可保护的项类型为 SQLInstance
5. 所有默认 SQL 数据库 (master、model、msdb), 其下的 sql server-0-可保护项类型为 Backup-sqldatabase
6. Sql server 1-可保护的项类型为 Backup-sqldatabase 的任何默认 SQL 数据库 (master、model、msdb)

如果[列出了备份容器](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0), 则 sql-server 0 的 sql-server 1 还将作为 "AzureVMAppContainer" 列出。

只需提取相关的 SQL 数据库即可[启用备份](#configuring-backup),[即席备份](#on-demand-backup)和[还原 PS cmdlet](#restore-sql-dbs)完全相同。
