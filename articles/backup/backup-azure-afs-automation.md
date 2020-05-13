---
title: 使用 PowerShell 备份 Azure 文件共享
description: 本文介绍如何使用 Azure 备份服务和 PowerShell 备份 Azure 文件文件共享。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 53187152802908e94ee4a8a231d3b7874cf42422
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199338"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>使用 PowerShell 备份 Azure 文件共享

本文介绍如何使用 Azure PowerShell 通过[Azure 备份](backup-overview.md)恢复服务保管库备份 azure 文件文件共享。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 设置 PowerShell 并注册恢复服务提供程序。
> * 创建恢复服务保管库。
> * 配置 Azure 文件共享的备份。
> * 运行备份作业。

## <a name="before-you-start"></a>开始之前

* [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
* 查看 Azure 库中的 Microsoft.recoveryservices [cmdlet 引用](/powershell/module/az.recoveryservices)引用。
* 查看恢复服务的以下 PowerShell 对象层次结构：

  ![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>设置 PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

按如下所述设置 PowerShell：

1. [下载最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)。

    > [!NOTE]
    > 备份 Azure 文件共享所需的最低 PowerShell 版本为 Az. Microsoft.recoveryservices 2.6.0。 使用最新版本或至少最小版本有助于避免现有脚本出现问题。 使用以下 PowerShell 命令安装最低版本：
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. 使用以下命令查找适用于 Azure 备份的 PowerShell cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 备份、Azure Site Recovery 和恢复服务保管库的别名和 cmdlet。 下面是可能显示的内容示例。 此 cmdlet 列表并不完整。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用“Connect-AzAccount”登录到 Azure 帐户****。
5. 在出现的网页上，系统会提示输入帐户凭据。

    或者，可以使用 **-Credential**将帐户凭据作为参数包含在**AzAccount** cmdlet 中。
   
    如果你是代表租户的 CSP 合作伙伴，请将该客户指定为租户。 使用其租户 ID 或租户主域名。 例如**AzAccount-租户 "fabrikam.com"**。

6. 将想要使用的订阅与帐户相关联，因为一个帐户可以有多个订阅：

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 如果是首次使用 Azure 备份，请使用**AzResourceProvider** Cmdlet 将 Azure 恢复服务提供程序注册到订阅：

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 验证提供程序是否已成功注册：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令输出中，确认 **RegistrationState** 是否更改为 **Registered**。 如果不是，请再次运行 **Register-AzResourceProvider** cmdlet。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用**AzResourceGroup** cmdlet 创建资源组。 创建资源组时，请指定该资源组的名称和位置。

按照以下步骤创建恢复服务保管库：

1. 如果没有现有的资源组，请使用[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) cmdlet 创建一个新的资源组。 在此示例中，我们将在美国西部区域创建一个资源组：

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. 使用 [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 创建保管库。 指定与用于资源组的保管库相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定保管库存储使用的冗余类型。 可以使用[本地冗余存储](../storage/common/storage-redundancy-lrs.md)或[异地冗余存储](../storage/common/storage-redundancy-grs.md)。
   
   下面的示例将**testvault**设置为**GeoRedundant**的[AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmdlet 的 **-BackupStorageRedundancy**选项设置为：

   ```powershell
   $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
   Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
   ```

### <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用[AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)：

```powershell
Get-AzRecoveryServicesVault
```

输出如下所示。 请注意，输出提供了关联的资源组和位置。

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

许多 Azure 备份 cmdlet 要求将恢复服务保管库对象用作输入，因此可以方便地在变量中存储保管库对象。

保管库上下文是在保管库中受保护的数据的类型。 使用[AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)设置。 设置上下文后，它将应用于所有后续 cmdlet。

下面的示例为**testvault**设置保管库上下文：

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保管库 ID

我们计划根据 Azure PowerShell 准则弃用保管库上下文设置。 可以改为存储或提取保管库 ID，并将其传递给相关命令。 如果尚未设置保管库上下文，或者想要为某个保管库指定运行该命令，请按如下所示将保管库 ID 传递 `-vaultID` 到所有相关的命令：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略指定备份计划，以及保留备份恢复点的时间。

一个备份策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。 可以配置每日、每周、每月或每年保留的备份。

下面是一些用于备份策略的 cmdlet：

* 使用[AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)查看默认的备份策略保持期。
* 使用[AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)查看默认备份策略计划。
* 使用[AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)创建新的备份策略。 输入计划和保留策略对象作为输入。

默认情况下，在计划策略对象中定义开始时间。 请使用以下示例将开始时间更改为所需的开始时间。 所需的开始时间应为协调世界时（UTC）。 该示例假设每日备份的所需开始时间为 01:00 AM UTC。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 只需在30分钟的倍数内提供开始时间。 在前面的示例中，它只能是 "01:00:00" 或 "02:30:00"。 开始时间不能为 "01:15:00"。

以下示例将计划策略和保留策略存储在变量中。 然后，它使用这些变量作为新策略 (**NewAFSPolicy**) 的参数。 “NewAFSPolicy”进行每日备份，并将其保留 30 天****。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出与下面类似：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>启用备份

定义备份策略后，可以使用策略为 Azure 文件共享启用保护。

### <a name="retrieve-a-backup-policy"></a>检索备份策略

您可以使用[AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)提取相关的策略对象。 使用此 cmdlet 可查看与工作负荷类型关联的策略，或获取特定策略。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>检索针对某个工作负荷类型的策略

以下示例检索工作负荷类型**AzureFiles**的策略：

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

输出与下面类似：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中**BackupTime**字段的时区采用 UTC 格式。 在 Azure 门户中显示备份时间时，该时间会根据本地时区进行调整。

#### <a name="retrieve-a-specific-policy"></a>检索特定的策略

以下策略检索名为**dailyafs**的备份策略：

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>启用保护并应用策略

使用[AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)启用保护。 将策略与保管库关联后，系统会根据策略计划触发备份。

下面的示例为存储帐户**testStorageAcct**中的 Azure 文件共享**testAzureFileShare**启用了保护，并提供了策略**dailyafs**：

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令将等待，直到完成配置保护作业，并提供类似于以下示例的输出：

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice-backup-item-identification"></a>重要说明：备份项标识

本部分概述了对 Azure 文件共享的备份的重要更改，以便为正式发布做准备。

为 Azure 文件共享启用备份时，用户为客户提供了一个文件共享名称作为实体名称，并且创建了一个备份项。 备份项的名称是 Azure 备份服务创建的唯一标识符。 标识符通常是用户友好名称。 但是，若要处理软删除的情况，可以删除文件共享，并使用同一名称创建另一个文件共享，Azure 文件共享的唯一标识现在为 ID。 

若要了解每个项的唯一 ID，请运行带有适用于**backupManagementType**和**WorkloadType**的相关筛选器的**AzRecoveryServicesBackupItem**命令，以获取所有相关项。 然后观察返回的 PowerShell 对象/响应中的名称字段。 

建议列出项，然后从响应的 "名称" 字段中检索它们的唯一名称。 使用此值可以筛选*名称*参数为的项。 否则，请使用*FriendlyName*参数检索具有其 ID 的项。

> [!IMPORTANT]
> 请确保将 PowerShell 升级到 Azure 文件共享备份的最低版本（Microsoft.recoveryservices 2.6.0）。 在此版本中， *FriendlyName*筛选器可用于**AzRecoveryServicesBackupItem**命令。 
>
> 将 Azure 文件共享的名称传递给*FriendlyName*参数。 如果将文件共享的名称传递给*name*参数，此版本将引发警告以将名称传递到*FriendlyName*参数。 
>
> 如果未安装最小版本，则可能导致现有脚本失败。 使用以下命令安装 PowerShell 的最低版本：
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>触发按需备份

使用[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0)为受保护的 Azure 文件共享运行按需备份：

1. 使用[AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)从保管库中的容器中检索存储帐户，其中保存了备份数据。
2. 若要启动备份作业，请使用[AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)获取有关 Azure 文件共享的信息。
3. 使用[AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)运行按需备份。

按如下所示运行按需备份：

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

命令返回具有要跟踪的 ID 的作业，如以下示例中所示：

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

执行备份时，将使用 Azure 文件共享快照。 通常，该作业在命令返回此输出时完成。

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure 门户中备份 Azure 文件](backup-afs.md)。
- 请参阅[GitHub 上的示例脚本](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup)，使用 Azure 自动化 runbook 来计划备份。
