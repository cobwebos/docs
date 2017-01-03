---
title: "将 Azure SQL 数据库备份存储 10 年之久 | Microsoft 文档"
description: "了解 Azure SQL 数据库如何支持将备份存储长达 10 年。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 92a2cca71380ac609e900d223908eda1a40be635
ms.openlocfilehash: 17de9fd999b904b66c4e9d574fb4754069ae133a


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>将 Azure SQL 数据库备份存储 10 年之久
出于法规要求、符合性或其他商业目的，许多应用程序要求保留 SQL 数据库的[自动备份](sql-database-automated-backups.md)功能所提供过去 7-35 天的自动完整数据库备份。

使用**长期备份保留**功能，可将 Azure SQL 数据库备份存储在 Azure 恢复服务保管库中至多 10 年。 每个保管库可存储至多 1000 个数据库。 可选择保管库中的任何备份，并将其还原为新数据库。

> [!NOTE]
> 24 小时内，每个保管库最多可启用 200 个数据库。 因此，建议对每个服务器使用单独的保管库，将此限制的影响降至最低。 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>SQL 数据库长期保留的工作原理是什么？

通过长期保留备份，可将 Azure SQL 数据库服务器与 Azure 恢复服务保管库相关联。 

* 必须在创建 SQL Server 的 Azure 订阅中，在相同地理区域和资源组内创建保管库。 
* 然后为任何数据库配置保留策略。 使用该策略时，会将每周完整数据库备份复制到恢复服务保管库，并在指定的保留期（至多 10 年）内保留此数据。 
* 此后，可从其中任意备份还原到订阅中任意服务器内的新数据库。 由 Azure 存储从现有备份进行复制，不会对现有数据库的性能造成影响。

## <a name="how-do-i-enable-long-term-retention"></a>如何启用长期保留？

为数据库配置长期备份保留：

1. 在与 SQL 数据库服务器相同的区域、订阅和资源组中创建 Azure 恢复服务保管库。 
2. 将服务器注册到保管库
3. 创建 Azure 恢复服务保护策略
4. 向需要长期备份保留的数据库应用保护策略

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>如何还原通过长期保留功能存储的数据库？

从长期保留备份恢复：

1. 列出存储备份的保管库
2. 列出映射到逻辑服务器的容器
3. 列出映射到数据库的保管库中的数据源
4. 列出可还原的恢复点
5. 从恢复点还原到订阅中的目标服务器

## <a name="how-much-does-long-term-retention-cost"></a>长期保留的价格是多少？

Azure SQL 数据库的长期保留按 [Azure 备份服务定价费率](https://azure.microsoft.com/pricing/details/backup/)收费。

将 Azure SQL 数据库服务器注册到保管库后，将对保管库中存储的每周备份所使用的存储空间总量进行收费。

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>在 Azure 门户中配置长期保留

在 Azure SQL 数据库服务器边栏选项卡上，可配置长期保留并根据需要创建 Azure 恢复服务保管库。

## <a name="configuring-long-term-retention-using-powershell"></a>使用 PowerShell 配置长期保留

通过以下步骤，使用 PowerShell 配置长期保留。
1. 创建恢复服务保管库
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. 将 Azure SQL 数据库服务器注册到保管库，以便长期存储服务器内数据库的备份。
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. 为存储备份创建保留策略。
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. 对要在保管库中存储其备份的 SQL 数据库启用长期保留。
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. 列出与保管库关联的服务器。 每个服务器都与保管库中的特定容器关联。 可通过运行以下命令列出已注册的服务器。
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. 列出容器中具有保留策略的数据库。 每个数据库在相应容器中都有一个相关备份项。 备份项名称派生自数据库名称。
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>从长期保留备份恢还原

使用以下步骤从 Azure 恢复服务保管库中的备份还原数据库：

1. 查找与 SQL Server 关联的恢复服务容器。
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. 查找与数据库关联的备份项。
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. 查找要从其还原的备份。
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. 从恢复点还原到新的 Azure SQL 数据库。
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>禁用长期保留

恢复服务将根据提供的保留策略自动清理备份。 

* 若要停止向保管库发送特定数据库的备份，请删除该数据库的保留策略。
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 保管库中已有的备份不会受到影响。 恢复服务将在其保留期到期时将其自动删除。
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>从 Azure 恢复服务保管库删除备份

从保管库手动删除备份。

1. 确定保管库中与“myserver”对应的容器
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. 确定要删除的备份项。
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. 删除备份项（数据库“mydb”的所有备份）
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. 删除与“myserver”关联的容器
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>长期保留常见问题解答：

1. 问：可否手动删除保管库中的特定备份？
   答：目前不可以，保管库将在保留期到期后自动清理备份。
2. 问：可否将服务器注册为将备份存储到多个存储库？
   答：不可以，目前，在同一时间只能将备份存储到 1 个保管库。
3. 问：保管库和服务器可否位于不同订阅中？
   答：不可以，目前，保管库和服务器必须位于相同的订阅和资源组中。
4. 问：可否使用在服务器所在区域之外创建的保管库？
   答：不可以，保管库和服务器必须位于同一区域，以便尽量减少复制时间、节省流量费。
5. 问：1 个保管库中可存储多少个数据库？
   答：目前，每个保管库仅支持至多 1000 个数据库。 
6. 问： 每个订阅可创建多少保管库？答：每个订阅最多可创建 25 个保管库。
7. 问： 每个保管库每天可配置多少个数据库？答：每个保管库每天只能设置 200 个数据库。
8. 问：长期保留可否用于于弹性数据库池？
   答：是的。 可为池中任意数据库配置保留策略。
9. 问：可否选择创建备份的时间？
   答：不可以，由 SQL 数据库控制备份计划，以便尽量减少对数据库性能的影响。
10. 问：我为数据库启用了 TDE。 可否从保管库将其还原？答：可以，支持 TDE。 即使原始数据库不复存在，也可从保管库还原该数据库。
11. 问： 如果订阅被挂起，保管库中的备份会出现什么情况？答：如果订阅被挂起，将保留现有数据库和备份，但不会将新的备份复制到保管库。 重新激活订阅后，该服务会继续将备份复制到保管库。 使用订阅挂起前复制到保管库的备份的还原操作可访问保管库。 
12. 问：可否访问 SQL 数据库备份文件，以便下载/还原到 SQL Server？
   答：目前不行。
13. 问：SQL 保留策略中可否存在多个计划（日计划、周计划、月计划、年计划）？
   答：不可以，目前这仅适用于虚拟机备份。

## <a name="next-steps"></a>后续步骤
数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。




<!--HONumber=Nov16_HO4-->


