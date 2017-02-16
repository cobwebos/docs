---
title: "使用 PowerShell 将 Azure SQL 数据库存档到 BACPAC 文件"
description: "使用 PowerShell 将 Azure SQL 数据库存档到 BACPAC 文件"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: ebbb31eb9387d68afab7559a3827682ed2551d5a
ms.openlocfilehash: de0b000b56ea90caeb1e2aa9a0b8c87e25c7c237


---
# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>使用 PowerShell 将 Azure SQL 数据库存档到 BACPAC 文件
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

本文说明了如何使用 PowerShell 将 Azure SQL 数据库存档到 [BACPAC 文件](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)（存储在 Azure Blob 存储中）。

需要创建 Azure SQL 数据库的存档时，可以将数据库架构和数据导出到 BACPAC 文件。 BACPAC 文件只是一个扩展名为 .bacpac 的 ZIP 文件。 BACPAC 文件稍后可存储在 Azure Blob 存储中或本地位置的本地存储中。 它还可重新导入到 Azure SQL 数据库或 SQL Server 本地安装中。

## <a name="considerations"></a>注意事项

* 为保证存档的事务处理一致，必须确保导出期间无写入活动，或者将从 Azure SQL 数据库的[事务处理一致性副本](sql-database-copy.md)中导出。
* 存档到 Azure Blob 存储的 BACPAC 文件的大小上限为 200 GB。 可使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示实用工具将更大的 BACPAC 文件存到本地存储。 此实用程序随 Visual Studio 和 SQL Server 一起提供。 你还可以[下载](https://msdn.microsoft.com/library/mt204009.aspx)最新版本的 SQL Server Data Tools 以获取此实用程序。
* 不支持使用 BACPAC 文件存档到 Azure 高级存储。
* 如果导出操作超过 20 个小时，可能会取消操作。 为提高导出过程中的性能，你可以进行如下操作：
  * 暂时提高服务级别。
  * 在导出期间终止所有读取和写入活动。
  * 对所有大型表格上的非 null 值使用[聚集索引](https://msdn.microsoft.com/library/ms190457.aspx)。 如果不使用聚集索引，当时间超过 6-12 个小时时，导出可能会失败。 这是因为导出服务需要完成表格扫描，才能尝试导出整个表格。 确认表格是否针对导出进行优化的一个好方法是，运行 **DBCC SHOW_STATISTICS** 并确保 *RANGE_HI_KEY* 不是 null 并且值分布良好。 相关详细信息，请参阅 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 不能用于备份和还原操作。 Azure SQL 数据库会自动为每个用户数据库创建备份。 有关详细信息，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
> 
> 

若要完成本文，需要以下各项：

* Azure 订阅。
* Azure SQL 数据库。
* [Azure 标准存储帐户](../storage/storage-create-storage-account.md)，具有可在标准存储中存储 BACPAC 的 blob 容器。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>导出数据库
[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx) cmdlet 向服务提交导出数据库请求。 根据数据库的大小，导出操作可能需要一些时间才能完成。

> [!IMPORTANT]
> 若要确保获得事务处理一致的 BACPAC 文件，应首先[创建数据库的副本](sql-database-copy-powershell.md)，然后导出该数据库副本。
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>监视导出操作的进度
运行 [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) 后，可运行 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx) 来查看请求的状态。 如果请求后立即运行，通常会返回“**状态: 处理中**”。 显示“状态: 成功”时，表示导出完毕。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>导出 SQL 数据库示例
以下示例将现有 SQL 数据库导出到 BACPAC，随后显示如何查看导出操作的状态。

若要运行示例，需将几个变量替换为数据库和存储帐户中的特定值。 在 [Azure 门户](https://portal.azure.com)中，浏览到存储帐户，以获取存储帐户名称、blob 容器名称和密钥值。 可单击存储帐户边栏选项卡上的“**访问密钥**”查找密钥。

将以下 `VARIABLE-VALUES` 替换为特定 Azure 资源中的值。 数据库名称就是要导出的现有数据库。

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>使用 Azure 自动化自动导出

Azure SQL 数据库自动导出现在处于预览状态，将在 2017 年 3 月 1 日停用。 从 2016 年 12 月 1 日开始，你将无法再对任何 SQL 数据库配置自动导出。 所有现有自动导出作业将继续正常运行，直到 2017 年 3 月 1 日。 2016 年 12 月 1 日之后，你可以使用[长期备份保留](sql-database-long-term-retention.md)或 [Azure 自动化](../automation/automation-intro.md)，以根据所选计划定期使用 PowerShell 存档 SQL 数据库。 对于示例脚本，可以从 [Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) 下载示例脚本。 


## <a name="next-steps"></a>后续步骤
* 若要了解如何使用 Powershell 导入 Azure SQL 数据库，请参阅[使用 PowerShell 导入 BACPAC](sql-database-import-powershell.md)。

## <a name="additional-resources"></a>其他资源
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)




<!--HONumber=Dec16_HO3-->


