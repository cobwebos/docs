---
title: "使用 PowerShell 复制 Azure SQL 数据库 | Microsoft 文档"
description: "使用 PowerShell 创建 Azure SQL 数据库的副本"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: edfbc0d94d9d5b33a25788772a98173187e394b8
ms.openlocfilehash: 031a17d179cee8d6cceb9ed3aefd993add1958b8
ms.lasthandoff: 02/11/2017


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>使用 PowerShell 复制 Azure SQL 数据库

本文介绍如何使用 PowerShell 将 SQL 数据库复制到同一服务器或不同服务器，或者将数据库复制到[弹性池](sql-database-elastic-pool.md)。 数据库复制操作使用 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) cmdlet。 

> [!NOTE]
> 还可使用 [Azure 门户](sql-database-copy-portal.md)或 [Transact-SQL](sql-database-copy-transact-sql.md) 复制 SQL 数据库。
>

若要完成本文，需要以下各项：

* Azure SQL 数据库（要复制的数据库）。 如果你没有 SQL 数据库，请按照[创建你的第一个 Azure SQL 数据库](sql-database-get-started.md)文章中的步骤创建一个。
* 最新版本的 Azure PowerShell。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

SQL 数据库的许多新功能仅在使用 [Azure Resource Manager 部署模型](../azure-resource-manager/resource-group-overview.md)时才可用，因此示例使用面向 Resource Manager 的 [Azure SQL 数据库 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)。 支持现有的经典部署模型 [Azure SQL 数据库（经典）cmdlet](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) 以实现向后兼容性，但建议使用 Resource Manager cmdlet。

> [!NOTE]
> 根据数据库的大小，复制操作可能需要一些时间才能完成。
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>将 SQL 数据库复制到同一台服务器
若要在同一服务器上创建副本，请省略 `-CopyServerName` 参数（或将其设置为同一服务器）。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>将 SQL 数据库复制到不同的服务器
若要在另一服务器上创建副本，请包括 `-CopyServerName` 参数并将其设置为另一服务器。 *副本*服务器必须已存在。 如果该服务器位于另一资源组中，则还必须包括 `-CopyResourceGroupName` 参数。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-pool"></a>将 SQL 数据库复制到弹性池
若要在池中创建 SQL 数据库的副本，请将 `-ElasticPoolName` 参数设置为现有的池。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>解析登录名
若要在复制操作完成后解析登录名，请参阅[解析登录名](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="example-powershell-script"></a>PowerShell 脚本示例
以下脚本假定所有资源组、服务器和池均已存在（请将变量值替换为现有的资源）。 所有项目必须都存在，数据库副本除外。

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId


    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"

    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"

    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName

    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName

    # Copy a database into an elastic pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>后续步骤
* 若要了解如何在将数据库复制到其他逻辑服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库的安全性](sql-database-geo-replication-security-config.md)。
* 若要使用 PowerShell 将数据库导出到 BACPAC 文件，请参阅[使用 PowerShell 将数据库导出到 BACPAC](sql-database-export-powershell.md)。
* [业务连续性概述](sql-database-business-continuity.md)
* [SQL 数据库文档](https://azure.microsoft.com/documentation/services/sql-database/)

## <a name="additional-resources"></a>其他资源
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [管理登录名](sql-database-manage-logins.md)
* [使用 SQL Server Management Studio 连接到 SQL 数据库并执行示例 T-SQL 查询](sql-database-connect-query-ssms.md)
* [将数据库导出到 BACPAC](sql-database-export.md)
* [业务连续性概述](sql-database-business-continuity.md)
* [SQL 数据库文档](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL 数据库 PowerShell cmdlet 参考](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)


