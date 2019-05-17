---
title: Azure 备份：备份和还原使用 Azure 备份和 PowerShell 的 Azure Vm 中的 SQL 数据库
description: 备份和还原使用 Azure 备份和 PowerShell 的 Azure Vm 中的 SQL 数据库。
services: backup
author: pvrk
manager: vijayts
keywords: Azure 备份;SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6d17d5c2c0eaebc694abe820318f6ac0c70b0be8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544608"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>备份和还原使用 PowerShell 的 Azure Vm 中的 SQL 数据库

本文介绍如何使用 Azure PowerShell 来备份和恢复 Azure VM 使用 SQL DB [Azure 备份](backup-overview.md)恢复服务保管库。

本教程介绍了如何完成以下操作：

> [!div class="checklist"]
> * 设置 PowerShell，并将注册 Azure 恢复服务提供程序。
> * 创建恢复服务保管库。
> * 针对 Azure 虚拟机内的 SQL 数据库配置备份。
> * 运行备份作业。
> * 还原备份 SQL 数据库。
> * 监视备份和还原作业。

## <a name="before-you-start"></a>开始之前

* [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
* 阅读有关的功能[备份 Azure Vm 中 SQL Db](backup-azure-sql-database.md#before-you-start)。
* 查看恢复服务的 PowerShell 对象层次结构。

### <a name="recovery-services-object-hierarchy"></a>恢复服务对象层次结构

下图汇总了对象层次结构。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 库中的 **Az.RecoveryServices** [cmdlet 参考](/powershell/module/az.recoveryservices)。

### <a name="set-up-and-install"></a>设置和安装

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

设置 PowerShell，如下所示：

1. [下载最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)。 所需的最低版本是 1.5.0。

2. 查找与此命令的 Azure 备份 PowerShell cmdlet:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 备份和恢复服务保管库的别名和 cmdlet。 下面是你可能会看到的示例。 它不是 cmdlet 的完整列表。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 登录到你的 Azure 帐户**Connect AzAccount**。
5. 在显示 web 页上，系统会提示输入你的帐户凭据。

    * 或者，可以将帐户凭据包含作为参数传入**Connect AzAccount** cmdlet 并结合 **-凭据**。
    * 如果你是租户的 CSP 合作伙伴，请将客户指定为使用其 tenantID 或租户主域名对租户。 例如，Connect-AzAccount -Tenant fabrikam.com。

6. 你想要使用的帐户，因为一个帐户可以有多个订阅的订阅关联。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 首次使用 Azure 备份时，请使用 Register-AzResourceProvider cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 验证已成功注册提供程序：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令输出中，确认**RegistrationState**更改为**已注册**。 如果没有打开，请运行**寄存器 AzResourceProvider**再次 cmdlet。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

请按照以下步骤创建恢复服务保管库。

恢复服务保管库是一种资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用 New-AzResourceGroup cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。

1. 保管库位于资源组中。 如果您不具备现有的资源组中，创建一个具有新[新建 AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)。 在此示例中，我们将创建新的资源组在美国西部区域中。

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 使用[新建 AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 来创建保管库。 请为保管库指定与资源组相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定要用于保管库存储冗余类型。

    * 可以使用[本地冗余存储](../storage/common/storage-redundancy-lrs.md)或[异地冗余存储](../storage/common/storage-redundancy-grs.md)。
    * 下面的示例设置 **-BackupStorageRedundancy**选项[集 AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty)为 cmd **testvault**设置为**异地冗余**。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

类似于以下输出。 提供了关联的资源组和位置。

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

* 许多 Azure 备份 cmdlet 需要恢复服务保管库对象作为输入，，因此可以方便地存储在变量中的保管库对象。
* 保管库上下文是在保管库中受保护的数据的类型。 使用设置该[集 AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)。 设置的上下文后，它适用于所有后续 cmdlet。

以下示例为 testvault 设置保管库上下文。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取的保管库 ID

我们计划弃用按照 Azure PowerShell 指南设置保管库上下文。 相反，可以存储或提取保管库 ID，并将其传递给相关命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略指定备份计划，并应保留多长时间的备份恢复点：

* 一个备份策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。
* 默认备份策略保持期使用视图[Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)。
* 默认备份策略计划使用的视图[Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)。
* 您使用[新建 AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet 创建新的备份策略。 输入计划和保留策略对象。

以下示例将计划策略和保留策略存储在变量中。 然后，它使用这些变量作为参数的新策略 (**NewSQLPolicy**)。 **NewSQLPolicy**采用每日"Full"备份，将其保留 180 天，并将每隔 2 小时的日志备份

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

类似于以下输出。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>启用备份

### <a name="registering-the-sql-vm"></a>注册 SQL VM

对于 Azure VM 备份和 Azure 文件共享，备份服务可以连接到这些 Azure 资源管理器资源并提取相关的详细信息。 由于 SQL Azure VM 中的应用程序，备份服务必须有权访问该应用程序和提取必要的详细信息。 为此，您需要向*注册*包含 SQL 应用程序使用恢复服务保管库的 Azure VM。 SQL VM 注册到保管库，你可以保护到该保管库 SQL 数据库。 使用[寄存器 AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0)的 PS cmdlet 来注册 VM。

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

该命令将返回此资源的备份容器，并且状态将注册

> [!NOTE]
> 如果未指定 force 参数，用户需要确认的文本要禁用对此容器的保护。 请忽略此文本，然后说"Y"以确认。 这是一个已知的问题，我们正在努力删除 force 参数的要求和的文本

### <a name="fetching-sql-dbs"></a>提取 SQL Db

完成注册后，备份服务将会列出在 VM 中所有可用的 SQL 组件。 若要查看所有 SQL 组件，但若要备份到此保管库，请使用[Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

输出将显示所有不受保护的 SQL 组件注册到此保管库使用项的类型和服务器名称的所有 SQL vm。 您可以进一步筛选到特定的 SQL VM 通过传递的容器参数或使用 Name 和 ServerName 以及 ItemType 的组合标志来实现此唯一 SQL 项。

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>配置备份

现在，我们具有所需的 SQL DB 和与策略的 it 需要进行备份，我们可以使用[启用 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet 来配置针对此 SQL 数据库的备份。

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

计算机注册后，备份服务将提取然后可用数据库的详细信息。 如果用户稍后将 SQL Db/SQL 实例添加到已注册的计算机，需要手动触发备份服务来执行查询以获取所有未受保护的数据库 （包括新添加的） 的全新试。 使用[Initialize AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 来执行的最新的查询在 SQL VM 上。 该命令将等到直到操作完成。 更高版本使用[Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 来获取最新版本不受保护的 SQL 组件的列表

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

一旦提取相关的可保护项，启用备份中所述[上面部分](#configuring-backup)。
如果其中一个不想要手动检测新的数据库，也可以选择自动保护使如下所述[如下](#enable-autoprotection)。

## <a name="enable-autoprotection"></a>启用自动保护使

用户可以配置备份，以便在将来添加的所有数据库将被自动都保护某些策略。 若要启用自动保护使，请使用[启用 AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet。

由于该指令是在 SQLInstance 完成所有未来数据库，该操作的备份级别。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

一旦给定自动保护使意向，插入系统，提取新查询添加数据库都将作为计划的后台任务每隔 8 小时。

## <a name="restore-sql-dbs"></a>还原 SQL 数据库

Azure 备份可以还原 SQL Server 数据库上运行的 Azure Vm，如下所示：

1. 使用事务日志备份还原到特定的日期或时间 （针对第二个）。 Azure 备份会自动确定适当的完整差异备份和还原所需的日志备份链基于所选的时间。
2. 还原某个特定的完整或差异备份，以便还原到特定恢复点。

检查先决条件所述[此处](restore-sql-database-azure-vm.md#prerequisites)还原 SQL 数据库之前。

第一次提取相关 SQL 数据库使用备份[Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>提取相应的还原时间

如上所述，用户可以将备份 SQL 数据库还原到完整/差异复制**或**到日志中的时间点。

#### <a name="fetch-distinct-recovery-points"></a>提取不同的恢复点

使用[Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0)备份 SQL 数据库中提取不同 （完整/差异） 的恢复点。

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = Get-Date.ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

输出结果类似于下面的示例

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

使用 RecoveryPointId 筛选器或数组筛选器提取相关的恢复点。

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>提取时间点恢复点

如果用户想要将数据库还原到某个特定点的时间，使用[Get AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet。 该 cmdlet 返回的日期表示开始和结束时间不间断的连续日志链的该 SQL 备份项的列表。 需在时间点应在此范围内。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

输出将类似于下面的示例。

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

上面的输出意味着该用户可以还原到任意点的时间显示的开始时间和结束时间之间。 时间均采用 UTC。 构造任意点的时间在如上所示的范围内的 PS。

> [!NOTE]
> 当日志中的时间点还原为选择时，即起始点，从其还原数据库的完整备份，也无需指定用户。 Azure 备份服务将会负责处理整个恢复计划即的完整备份，若要选择的内容日志备份应用等。

### <a name="determine-recovery-configuration"></a>确定恢复配置

发生 SQL 数据库还原时，支持以下还原方案。

1. 重写具有来自另一个恢复点-OriginalWorkloadRestore 数据的备份 SQL 数据库
2. 在同一个 SQL 实例-AlternateWorkloadRestore 中新的数据库中还原 SQL 数据库
3. 另一个 SQL VM-AlternateWorkloadRestore 中的另一个 SQL 实例中的新数据库中还原 SQL 数据库

后提取相关的恢复点 (非重复文件或日志时间点)，使用[Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0)的 PS cmdlet 来提取根据所需的恢复计划的恢复配置对象。

#### <a name="original-workload-restore"></a>原始工作负荷还原

若要覆盖备份数据库的恢复点中的数据，只需指定正确的标志和相关的恢复点如以下示例中所示。

##### <a name="original-restore-with-distinct-recovery-point"></a>原始还原以不同的恢复点

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>原始日志时间点还原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>备用的工作负荷还原

> [!IMPORTANT]
> 备份 SQL 数据库可以还原到另一个 SQLInstance，新的数据库为已登记到此保管库的 Azure VM 中。

如上所述，如果目标 SQLInstance 位于另一个 Azure VM，请确保它是[注册到此保管库](#registering-the-sql-vm)和相关 SQLInstance 显示为一个可保护项。

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

然后只需传递相关的恢复点，使用正确的标志的目标 SQL 实例如下所示。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>以不同的恢复点的备用还原

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>备用日志时间点还原

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

从最后一个恢复点的配置对象获得[Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 用于还原的所有相关信息，因此，如下所示。

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

可以编辑已还原的数据库名称、 OverwriteWLIfpresent、 NoRecoveryMode 和 targetPhysicalPath 字段。 目标文件路径，如下所示获取更多详细信息。

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

将相关的 PS 属性设置为字符串值，如下所示。

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
> 请确保最终恢复 config 对象具有所有必要和适当值，因为还原操作将根据配置对象。

### <a name="restore-with-relevant-configuration"></a>还原与相关的配置

一旦获取并验证相关的恢复配置对象，使用[还原 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 来启动还原过程。

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

还原操作将返回一个作业来进行跟踪。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>管理 SQL 备份

### <a name="on-demand-backup"></a>按需备份

一旦已启用的数据库备份，用户还可以触发按需备份的数据库使用[备份 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet。 下面的示例使用已启用压缩会触发 SQL DB 上的完整备份和完整备份也应保留 60 天。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

即席备份命令返回的作业进行跟踪。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

如果该输出将丢失，或者你想要获取相关的作业 ID，[获取的作业列表](#track-azure-backup-jobs)从 Azure 备份服务，然后跟踪它和它的详细信息。

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
> 请务必阅读这[文档](backup-sql-server-azure-troubleshoot.md#re-registration-failures)以了解失败的症状和原因，然后再尝试重新注册

若要触发的 SQL VM 的重新注册，提取相关的备份容器并将其传递到注册 cmdlet。

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

如果自动保护使 SQLInstance 上进行配置，用户可以禁用它使用[禁用 AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS cmdlet。

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>注销 SQL VM

如果所有的数据库的 SQL server[是否存在受保护且不备份数据不再](#delete-backup-data)，用户可以注销从此保管库的 SQL VM。 只有这样用户可以保护到另一个保管库的数据库。 使用[注销 AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet 注销 SQL VM。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>跟踪 Azure 的备份作业

请务必注意，Azure 备份只跟踪 SQL 备份中的用户触发作业。 计划的备份 （包括日志备份） 将不显示在门户/powershell。 但是，如果任何计划的作业失败，[备份警报](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)生成并在门户中显示。 [使用 Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)来跟踪所有计划的作业和其他相关信息。

用户可以触发即席/用户使用跟踪操作中返回的 JobID[输出](#on-demand-backup)的异步作业，例如备份。 使用[Get AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail)的 PS cmdlet 来跟踪作业和其详细信息。

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

若要从 Azure 备份服务获取的即席作业和及其状态的列表，请使用[Get AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet。 以下示例返回所有进行中的 SQL 作业。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

若要取消正在进行中作业，请使用[停止 AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS cmdlet。

## <a name="managing-sql-always-on-availability-groups"></a>管理 SQL Alwayson 可用性组

对于 SQL Always On 可用性组，请确保向[注册的所有节点](#registering-the-sql-vm)的可用性组 (AG)。 完成注册后的所有节点，可保护项下以逻辑方式创建 SQL 可用性组对象。 SQL 可用性组下的数据库将被列为 sql 数据库。 节点将显示为独立的实例并将默认的 SQL 数据库在其下将列出为 SQL 数据库。

例如，让我们假定 SQL 可用性组具有两个节点:"sql server 0"和"sql server 1"和 1 的 SQL 可用性组数据库。 如果注册这两个节点后用户[列出了可保护项](#fetching-sql-dbs)，它会列出以下组件

1. SQL 可用性组对象的可保护项作为 SQLAvailabilityGroup 类型
2. 为 sql 数据库的 SQL 可用性组数据库-可保护项类型
3. sql server 0-SQLInstance 形式键入的可保护项
4. sql server 1-SQLInstance 为键入的可保护项
5. 在 sql server 0-任何默认 SQL 数据库 （master、 model、 msdb） 可保护项键入作为 sql Database
6. 在 sql server 1-任何默认 SQL 数据库 （master、 model、 msdb） 可保护项键入作为 sql Database

sql server 0，sql server 1 将还被列为"AzureVMAppContainer"时[列出备份容器](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)。

只需提取到相关的 SQL 数据库[启用备份](#configuring-backup)并[临时备份](#on-demand-backup)并[还原 PS cmdlet](#restore-sql-dbs)完全相同。
