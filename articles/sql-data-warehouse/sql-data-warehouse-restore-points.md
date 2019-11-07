---
title: 用户定义的还原点
description: 如何创建还原点 Azure SQL 数据仓库。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 61ceb2f1271d085321215aff1c6d138feb95d743
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692509"
---
# <a name="user-defined-restore-points"></a>用户定义的还原点

本文介绍如何使用 PowerShell 和 Azure 门户为 Azure SQL 数据仓库创建新的用户定义的还原点。

## <a name="create-user-defined-restore-points-through-powershell"></a>通过 PowerShell 创建用户定义的还原点

若要创建用户定义的还原点，请使用 [New-AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] PowerShell cmdlet。

1. 开始之前，请确保[安装 Azure PowerShell][Install Azure PowerShell]。
2. 打开 PowerShell。
3. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
4. 选择包含要还原的数据库的订阅。
5. 为数据仓库的即时复制创建还原点。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. 查看所有现有还原点的列表。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>通过 Azure 门户创建用户定义的还原点

还可以通过 Azure 门户创建用户定义的还原点。

1. 登录到 [Azure 门户][Azure portal]帐户。

2. 导航到要为其创建还原点的 SQL 数据仓库。

3. 从左窗格中选择“概述”，选择“+ 新建还原点”。 如果“新建还原点”按钮未启用，请确保数据仓库未暂停。

    ![新建还原点](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 为用户定义的还原点指定名称，然后单击“应用”。 用户定义的还原点的默认保留期为七天。

    ![还原点的名称](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>后续步骤

- [还原现有数据仓库][Restore an existing data warehouse]
- [还原已删除的数据仓库][Restore a deleted data warehouse]
- [从异地备份数据仓库还原][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
