---
title: 还原现有数据仓库
description: 还原现有 SQL 池的操作指南。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bf83de481cf15711da45edf00e63262a05489247
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199510"
---
# <a name="restore-an-existing-sql-pool"></a>还原现有的 SQL 池

本文介绍如何使用 Azure 门户和 PowerShell 在 Azure Synapse Analytics 中还原现有的 SQL 池。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个池都由一个具有默认 DTU 配额的 SQL server （例如，myserver.database.windows.net）托管。 验证 SQL server 是否有足够的剩余 DTU 配额来还原数据库。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="before-you-begin"></a>开始之前

1. 请确保[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 具有要从中还原的现有还原点。 如果要创建新的还原，请参阅[教程创建新的用户定义的还原点](sql-data-warehouse-restore-points.md)。

## <a name="restore-an-existing-sql-pool-through-powershell"></a>通过 PowerShell 还原现有的 SQL 池

若要从还原点还原现有 SQL 池，请使用[AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell cmdlet。

1. 打开 PowerShell。

2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。

3. 选择包含要还原的数据库的订阅。

4. 列出 SQL 池的还原点。

5. 使用 RestorePointCreationDate 选取所需的还原点。

6. 使用[AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell CMDLET 将 SQL 池还原到所需的还原点。
        1. 若要将 SQL 池还原到不同的逻辑服务器，请确保指定其他逻辑服务器名称。  此逻辑服务器还可以位于不同的资源组和区域。
        2. 若要还原到其他订阅，请使用 "移动" 按钮将逻辑服务器移到另一个订阅。

7. 验证还原的 SQL 池是否处于联机状态。

8. 还原完成后，可以按照在[恢复后配置数据库](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)中的配置恢复的 SQL 池。

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>通过 Azure 门户还原现有的 SQL 池

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到要从中还原的 SQL 池。
3. 在“概览”边栏选项卡顶部，选择“还原”。

    ![ 还原概述](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 选择“自动还原点”或“用户定义的还原点”。 如果 SQL 池没有任何自动还原点，请在还原前等待几个小时或创建用户定义的还原点。 对于用户定义的还原点，请选择现有的还原点或创建一个新的还原点。 对于 "**服务器**"，可以在不同的资源组和区域中选取逻辑服务器，也可以创建一个新的。 提供所有参数后，单击 "**查看 + 还原**"。

    ![自动还原点](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>后续步骤
- [还原已删除的 SQL 池](sql-data-warehouse-restore-deleted-dw.md)
- [从异地备份 SQL 池还原](sql-data-warehouse-restore-from-geo-backup.md)

 