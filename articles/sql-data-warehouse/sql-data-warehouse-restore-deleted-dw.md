---
title: 还原已删除的数据仓库
description: 如何还原已删除的 Azure SQL 数据仓库的指南。
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
ms.openlocfilehash: e508eff3b322b49a6dc50d818c8bcccc3e924ff2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759646"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>还原已删除的 Azure SQL 数据仓库

本文介绍如何使用 Azure 门户和 PowerShell 还原已删除的 SQL 数据仓库：

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 DTU 配额的 SQL 服务器（例如 myserver.database.windows.net）托管。  验证 SQL server 是否有足够的剩余 DTU 配额来还原数据库。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>通过 PowerShell 还原已删除的数据仓库

若要还原已删除的 SQL 数据仓库，请使用[AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) cmdlet。 如果还删除了相应的逻辑服务器，则无法还原该数据仓库。

1. 在开始之前，请确保[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的已删除数据仓库的订阅。
5. 获取特定的已删除数据仓库。
6. 还原已删除的数据仓库
    1. 若要将已删除的 SQL 数据仓库还原到不同的逻辑服务器，请确保指定其他逻辑服务器名称。  此逻辑服务器还可以位于不同的资源组和区域。
    1. 若要还原到其他订阅，请使用 "[移动](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal)" 按钮将逻辑服务器移到另一个订阅。
1. 验证已还原的数据仓库是否处于联机状态。
1. 还原完成后，可以按照在[恢复后配置数据库](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)中的配置恢复的数据仓库。

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>通过 Azure 门户还原已删除的数据库

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到已删除的数据仓库所托管的 SQL server。
3. 选择目录中的 "**已删除数据库**" 图标。

    ![已删除的数据库](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 选择要还原的已删除 SQL 数据仓库。

    ![选择“已删除的数据库”](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的**数据库名称**，然后单击 **"确定"**

    ![指定数据库名称](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>后续步骤
- [还原现有数据仓库](sql-data-warehouse-restore-active-paused-dw.md)
- [从异地备份数据仓库还原](sql-data-warehouse-restore-from-geo-backup.md)