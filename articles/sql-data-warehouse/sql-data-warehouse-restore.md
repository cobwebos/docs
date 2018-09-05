---
title: 还原 Azure SQL 数据仓库 | Microsoft Docs
description: 用于还原 SQL 数据仓库的操作指南。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 583346f2297f590d8e9484c0a3c19c947de7f740
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191595"
---
# <a name="restoring-azure-sql-data-warehouse"></a>还原 Azure SQL 数据仓库 
在本文中，你将学习如何执行以下操作：

- 创建还原点
- 从自动还原点或用户定义的还原点进行还原
- 从已删除的数据库进行还原
- 从异地备份进行还原
- 从用户定义的还原点创建数据仓库的副本

## <a name="before-you-begin"></a>开始之前
**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 DTU 配额的 SQL 服务器（例如 myserver.database.windows.net）托管。  在还原 SQL 数据仓库之前，请确保 SQL Server 的剩余 DTU 配额足够进行数据库还原。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改][Request a DTU quota change]。

# <a name="restore-through-powershell"></a>通过 Powershell 进行还原

## <a name="install-powershell"></a>安装 PowerShell
若要对 SQL 数据仓库使用 Azure PowerShell，需要安装 Azure PowerShell 1.0 或更高版本。  可通过运行 **Get-Module -ListAvailable -Name AzureRM** 来检查版本。  可通过 [Microsoft Web 平台安装程序][Microsoft Web Platform Installer]安装最新版本。  有关安装最新版本的详细信息，请参阅[如何安装和配置 Azure PowerShell][How to install and configure Azure PowerShell]。

## <a name="restore-an-active-or-paused-database"></a>还原活动或暂停的数据库
若要从还原点还原数据库，请使用 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] PowerShell cmdlet。

1. 打开 Windows PowerShell。

2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。

3. 选择包含要还原的数据库的订阅。

4. 列出数据库的还原点。

5. 使用 RestorePointCreationDate 选取所需的还原点。

   > [!NOTE]
   > 在还原时，可以指定不同的 ServiceObjectiveName (DWU) 或位于另一区域中的不同服务器。

6. 将数据库还原到所需的还原点。

7. 验证已还原的数据库是否处于联机状态。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> 完成还原后，即可按[在恢复后配置数据库][Configure your database after recovery]中的说明配置恢复的数据库。
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points"></a>使用用户定义的还原点复制数据仓库
若要从用户定义的还原点还原数据库，请使用 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] PowerShell cmdlet。

1. 打开 Windows PowerShell。
2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
3. 选择包含要还原的数据库的订阅。
4. 为数据库的即时复制创建还原点
5. 将数据库重命名为一个临时名称。
5. 根据指定的 RestorePointLabel 检索最新还原点。
6. 获取用于启动还原的数据库的资源 ID
6. 将数据库还原到所需的还原点。
7. 验证已还原的数据库是否处于联机状态。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database"></a>还原已删除的数据库
若要还原已删除的数据库，请使用 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] cmdlet。

1. 打开 Windows PowerShell。
2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
3. 选择包含要还原的已删除数据库的订阅。
4. 获取特定的已删除数据库。
5. 还原已删除的数据库。
6. 验证已还原的数据库是否处于联机状态。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> 完成还原后，即可按[在恢复后配置数据库][Configure your database after recovery]中的说明配置恢复的数据库。
>

## <a name="restore-from-an-azure-geographical-region"></a>从 Azure 地理区域还原
若要恢复数据库，请使用 [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase] cmdlet。

> [!NOTE]
> 可以执行到第 2 代的异地还原！ 若要执行此操作，请将一个第 2 代 ServiceObjectiveName（例如 DW1000**c**）指定为可选参数。
>

1. 打开 Windows PowerShell。
2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
3. 选择包含要还原的数据库的订阅。
4. 获取要恢复的数据库。
5. 创建对数据库的恢复请求。
6. 验证异地还原的数据库的状态。

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 若要在完成还原后配置数据库，请参阅[在恢复后配置数据库][Configure your database after recovery]。
>

如果源数据库启用了 TDE，则已恢复的数据库将启用 TDE。

# <a name="restore-through-the-azure-portal"></a>通过 Azure 门户进行还原

## <a name="create-a-user-defined-restore-point"></a>创建用户定义的还原点
1. 登录到 [Azure 门户][Azure portal]。

2. 导航到要为其创建还原点的 SQL 数据仓库。

3. 在“概览”边栏选项卡顶部，选择“+ 新建还原点”。

    ![新建还原点](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)
    
4. 为还原点指定一个名称。

    ![还原点的名称](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database"></a>还原活动或暂停的数据库
1. 登录到 [Azure 门户][Azure portal]。
2. 导航到要从中进行还原的 SQL 数据仓库。
3. 在“概览”边栏选项卡顶部，选择“还原”。

    ![ 还原概述](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)
    
4. 选择“自动还原点”或“用户定义的还原点”。

    ![自动还原点](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)
    
5. 对于用户定义的还原点，请**选择还原点**或**新建用户定义的还原点**。

    ![用户定义的还原点](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database"></a>还原已删除的数据库
1. 登录到 [Azure 门户][Azure portal]。
2. 导航到承载着已删除数据库的 SQL Server。
3. 在目录中选择“已删除的数据库”图标。

    ![已删除的数据库](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)
    
4. 选择要还原的已删除数据库。

    ![选择“已删除的数据库”](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)
    
5. 指定一个新的数据库名称。

    ![指定数据库名称](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
