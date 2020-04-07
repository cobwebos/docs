---
title: 还原现有数据仓库
description: 还原现有 SQL 池的指南。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6fa8bd42eb067124ab6ea1db77e2f3d6fba79638
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745210"
---
# <a name="restore-an-existing-sql-pool"></a>还原现有 SQL 池

在本文中，您将了解如何使用 Azure 门户和 PowerShell 在 Azure 突触分析中还原现有 SQL 池。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个池都由具有默认 DTU 配额的 SQL 服务器（例如myserver.database.windows.net）托管。 验证 SQL Server 的剩余 DTU 配额是否足够进行数据库还原。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="before-you-begin"></a>在开始之前

1. 确保[安装 Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 有一个现有的还原点，需要从该点进行还原。 若要创建新的还原，请参阅[教程：新建用户定义的还原点](sql-data-warehouse-restore-points.md)。

## <a name="restore-an-existing-sql-pool-through-powershell"></a>通过 PowerShell 还原现有 SQL 池

要从还原点还原现有 SQL 池，请使用[还原-AzSql 数据库](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)PowerShell cmdlet。

1. 打开 PowerShell。

2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。

3. 选择包含要还原的数据库的订阅。

4. 列出 SQL 池的还原点。

5. 使用 RestorePointCreationDate 选取所需的还原点。

6. 使用[还原-AzSql数据库](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)PowerShell cmdlet 将 SQL 池还原到所需的还原点。
        1. 要将 SQL 池还原到其他逻辑服务器，请确保指定其他逻辑服务器名称。  该逻辑服务器也可以位于另一资源组和区域中。
        2. 要还原到其他订阅，请使用"移动"按钮将逻辑服务器移动到其他订阅。

7. 验证还原的 SQL 池是否联机。

8. 还原完成后，可以通过在[恢复后按照配置数据库来配置](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)恢复的 SQL 池。

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>通过 Azure 门户还原现有 SQL 池

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到要还原的 SQL 池。
3. 在“概览”边栏选项卡顶部，选择“还原”****。

    ![ 还原概述](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 选择“自动还原点”或“用户定义的还原点”。******** 如果 SQL 池没有任何自动还原点，请等待几个小时或创建用户定义的还原点，然后再还原。 对于用户定义的还原点，请选择一个现有的，或者创建一个新的。 对于**服务器**，可以选取另一资源组和区域中的逻辑服务器，也可以创建一个新的。 在提供所有参数后，请单击“查看 + 还原”。****

    ![自动还原点](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>后续步骤

- [还原已删除的 SQL 池](sql-data-warehouse-restore-deleted-dw.md)
- [从异地备份 SQL 池还原](sql-data-warehouse-restore-from-geo-backup.md)
