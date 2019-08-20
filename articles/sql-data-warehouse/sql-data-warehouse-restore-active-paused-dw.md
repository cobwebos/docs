---
title: 还原现有的 Azure SQL 数据仓库 |Microsoft Docs
description: 还原现有 Azure SQL 数据仓库的操作方法指南。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7470dd96109823057a174d2ecf097583dcb51898
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575268"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>还原现有的 Azure SQL 数据仓库

本文介绍如何通过 Azure 门户和 PowerShell 还原现有的 SQL 数据仓库:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 DTU 配额的 SQL 服务器 (例如 myserver.database.windows.net) 托管。 验证 SQL server 是否有足够的剩余 DTU 配额来还原数据库。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改][Request a DTU quota change]。

## <a name="before-you-begin"></a>开始之前

1. 请确保[安装 Azure PowerShell][Install Azure PowerShell]。
2. 具有要从中还原的现有还原点。 如果要创建新的还原, 请参阅[教程创建新的用户定义的还原点][the tutorial to create a new user-defined restore point]。

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>通过 PowerShell 还原现有数据仓库

若要从还原点还原现有数据仓库, 请使用[AzSqlDatabase][Restore-AzSqlDatabase] PowerShell cmdlet。

1. 打开 PowerShell。

2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。

3. 选择包含要还原的数据库的订阅。

4. 列出数据仓库的还原点。

5. 使用 RestorePointCreationDate 选取所需的还原点。

6. 使用[AzSqlDatabase][Restore-AzSqlDatabase] PowerShell cmdlet 将数据仓库还原到所需的还原点。
        1. 若要将 SQL 数据仓库还原到不同的逻辑服务器, 请确保指定其他逻辑服务器名称。  此逻辑服务器还可以位于不同的资源组和区域。
        2. 若要还原到其他订阅, 请使用 "移动" 按钮将逻辑服务器移到另一个订阅。

7. 验证已还原的数据仓库是否处于联机状态。

8. 还原完成后, 可以按照在[恢复后配置数据库][Configure your database after recovery]中的配置恢复的数据仓库。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>通过 Azure 门户还原现有数据仓库

1. 登录到 [Azure 门户][Azure portal]。
2. 导航到要从中还原的 SQL 数据仓库。
3. 在“概览”边栏选项卡顶部，选择“还原”。

    ![ 还原概述](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 选择“自动还原点”或“用户定义的还原点”。 如果数据仓库没有任何自动还原点, 请在还原前等待几个小时或创建用户定义的还原点。 对于用户定义的还原点, 请选择现有的还原点或创建一个新的还原点。 对于 "**服务器**", 可以在不同的资源组和区域中选取逻辑服务器, 也可以创建一个新的。 提供所有参数后, 单击 "**查看 + 还原**"。

    ![自动还原点](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>后续步骤
- [还原已删除的数据仓库][Restore a deleted data warehouse]
- [从异地备份数据仓库还原][Restore from a geo-backup data warehouse]
 
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
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
