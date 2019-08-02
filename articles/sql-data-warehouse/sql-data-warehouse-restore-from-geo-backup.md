---
title: 从异地备份还原 Azure SQL 数据仓库 |Microsoft Docs
description: 有关异地还原 Azure SQL 数据仓库的操作方法指南。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68426650"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>异地还原 Azure SQL 数据仓库

本文介绍如何通过 Azure 门户和 PowerShell 从异地备份还原数据仓库。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 DTU 配额的 SQL 服务器 (例如 myserver.database.windows.net) 托管。 验证 SQL server 是否有足够的剩余 DTU 配额来还原数据库。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改][Request a DTU quota change]。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>通过 PowerShell 从 Azure 地理区域还原

若要从异地备份还原, 请使用[AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup]和[AzSqlDatabase][Restore-AzSqlDatabase] cmdlet。

> [!NOTE]
> 可以执行到第 2 代的异地还原！ 若要执行此操作，请将一个第 2 代 ServiceObjectiveName（例如 DW1000**c**）指定为可选参数。
>

1. 在开始之前, 请确保[安装 Azure PowerShell][Install Azure PowerShell]。
2. 打开 PowerShell。
2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
3. 选择包含要还原的数据仓库的订阅。
4. 获取要恢复的数据仓库。
5. 为数据仓库创建恢复请求。
6. 验证异地还原的数据仓库的状态。
7. 若要在完成还原后配置数据仓库，请参阅[在恢复后配置数据库][Configure your database after recovery]。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

如果源数据库启用了 TDE，则已恢复的数据库将启用 TDE。

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>通过 Azure 门户从 Azure 地理区域还原

按照以下概述的步骤从异地备份还原 Azure SQL 数据仓库:

1. 登录到你的[Azure 门户][Azure portal]帐户。
1. 单击 " **+ 创建资源**" 并搜索 "SQL 数据仓库", 然后单击 "**创建**"。

    ![新建 DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. 填写 "**基本**信息" 选项卡中所需的**信息, 然后单击 "下一步":其他设置**。

    ![基本](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. 对于 "**使用现有数据**" 参数, 请选择 "**备份**", 然后从向下滚动选项中选择相应的备份。 单击 "**查看 + 创建**"。
 
   ![备份](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. 还原数据仓库后, 请检查**状态**是否为 "联机"。

## <a name="next-steps"></a>后续步骤
- [还原现有数据仓库][Restore an existing data warehouse]
- [还原已删除的数据仓库][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
