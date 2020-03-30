---
title: 还原已删除的 SQL 池
description: 如何指导还原已删除的 SQL 池。
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
ms.openlocfilehash: 5f0432cafee07dbed071d24aa8c24ee9b2176967
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350180"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>使用 Azure 同步分析还原已删除的 SQL 池

在本文中，您将学习使用 Azure 门户或 PowerShell 还原 SQL。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个 SQL 池都由具有默认 DTU 配额的 SQL 服务器（例如myserver.database.windows.net）托管。  验证 SQL Server 的剩余 DTU 配额是否足够进行数据库还原。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>通过 PowerShell 还原已删除的数据仓库

要还原已删除的 SQL 池，请使用[还原-AzSql 数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)cmdlet。 如果相应的逻辑服务器也被删除，则不能还原该数据仓库。

1. 开始之前，请确保[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的已删除数据仓库的订阅。
5. 获取特定的已删除数据仓库。
6. 还原已删除的数据仓库
    1. 若要将已删除的 SQL 数据仓库还原到另一逻辑服务器，请确保指定另一逻辑服务器名称。  该逻辑服务器也可以位于另一资源组和区域中。
    1. 要还原到其他订阅，请使用["移动"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal)按钮将逻辑服务器移动到其他订阅。
1. 验证已还原的数据仓库是否处于联机状态。
1. 还原完成后，您可以通过在[恢复后按照配置数据库来配置](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)恢复的数据仓库。

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

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 导航到承载着已删除数据仓库的 SQL Server。
3. 在目录中选择 **"已删除的数据库**"图标。

    ![已删除的数据库](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 选择要还原的已删除 SQL 数据仓库。

    ![选择“已删除的数据库”](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的**数据库名称**，并单击“确定”****

    ![指定数据库名称](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>后续步骤
- [还原现有 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [从异地备份 SQL 池还原](sql-data-warehouse-restore-from-geo-backup.md)