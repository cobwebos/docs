---
title: "将 Azure SQL 数据库导出到 BACPAC 文件 | Microsoft Docs"
description: "使用 Azure 门户将 Azure SQL 数据库导出到 BACPAC 文件"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: eadd300dcda2f160589c5e8e4fb7508445ef9944
ms.lasthandoff: 04/10/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>将 Azure SQL 数据库导出到 BACPAC 文件

本文讨论如何将 Azure SQL 数据库导出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件。 本文讨论如何使用以下方法进行导出：
- [Azure 门户](https://portal.azure.com)
- [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令行实用工具
- [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) cmdlet
- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) 中的[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)向导。

> [!IMPORTANT] 
> Azure SQL 数据库自动导出现在处于预览状态，将在 2017 年 3 月 1 日停用。 从 2016 年 12 月 1 日开始，你将无法再对任何 SQL 数据库配置自动导出。 所有现有自动导出作业将继续正常运行，直到 2017 年 3 月 1 日。 2016 年 12 月 1 日之后，你可以使用[长期备份保留](sql-database-long-term-retention.md
)或 [Azure 自动化](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md)，以根据所选计划定期使用 PowerShell 存档 SQL 数据库。 对于脚本示例，可以从 Github 下载 [PowerShell 脚本示例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)。
>

> [!IMPORTANT] 
> Azure SQL 数据库自动导出现在处于预览状态，将在 2017 年 3 月 1 日停用。 从 2016 年 12 月 1 日开始，你将无法再对任何 SQL 数据库配置自动导出。 所有现有自动导出作业将继续正常运行，直到 2017 年 3 月 1 日。 2016 年 12 月 1 日之后，你可以使用[长期备份保留](sql-database-long-term-retention.md
)或 [Azure 自动化](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md)，以根据所选计划定期使用 PowerShell 存档 SQL 数据库。 对于脚本示例，可以从 Github 下载 [PowerShell 脚本示例](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)。
>

## <a name="overview"></a>概述

如果需要为存档或移动到其他平台而导出数据库，可将数据库架构和数据导出到 BACPAC 文件。 BACPAC 文件是一个扩展名为 BACPAC 的 ZIP 文件，它包含来自 SQL Server 数据库的元数据和数据。 可将 BACPAC 文件存储在 Azure blob 存储中或存储在本地位置的本地存储中，之后可以导入回 Azure SQL 数据库或 SQL Server 本地安装。 

> [!IMPORTANT]
> 如果将从 SQL Server 中导出作为迁移到 Azure SQL 数据库的准备，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
> 

## <a name="considerations"></a>注意事项

* 为保证导出的事务处理方式一致，须确保导出期间未发生写入活动，或者正在从 Azure SQL 数据库的[事务处理方式一致性副本](sql-database-copy.md)中导出。
* 如果计划导出到 blob 存储，则 BACPAC 文件的最大大小为 200 GB。 若要存档更大的 BACPAC 文件，请导出到本地存储。
* 本文介绍的方法不支持将 BACPAC 文件导出到 Azure 高级存储。
* 如果从 Azure SQL 数据库的导出操作超过 20 个小时，则可能会取消操作。 为提高导出过程中的性能，你可以进行如下操作：
  * 暂时提高服务级别。
  * 在导出期间终止所有读取和写入活动。
  * 对所有大型表格上的非 null 值使用[聚集索引](https://msdn.microsoft.com/library/ms190457.aspx)。 如果不使用聚集索引，当时间超过 6-12 个小时时，导出可能会失败。 这是因为导出服务需要完成表格扫描，才能尝试导出整个表格。 确认表格是否针对导出进行优化的一个好方法是，运行 **DBCC SHOW_STATISTICS** 并确保 *RANGE_HI_KEY* 不是 null 并且值分布良好。 相关详细信息，请参阅 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 不能用于备份和还原操作。 Azure SQL 数据库会自动为每个用户数据库创建备份。 有关详细信息，请参阅[业务连续性概述](sql-database-business-continuity.md)和 [SQL 数据库备份](sql-database-automated-backups.md)。  
> 

## <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户导出数据库，请打开数据库页，并在工具栏上单击“导出”。 指定 *.bacpac 文件名，为导出提供 Azure 存储帐户和容器，并提供用于连接到源数据库的凭据。  

   ![数据库导出](./media/sql-database-export/database-export.png)

若要监视导出操作的进度，请打开包含待导出数据库的逻辑服务器的相应页面。 向下滚动到“操作”，然后单击“导入/导出”历史记录。

## <a name="sqlpackage-utility"></a>SQLPackage 实用工具

若要使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令行实用工具导出 SQL 数据库，请参阅[导出参数和属性](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties)。 SQLPackage 实用工具附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

在大多数生产环境中，建议使用 SQLPackage 实用工具来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

SQL Server Management Studio 的最新版本也提供一个向导，可将 Azure SQL 数据库导出到 bacpac 文件。 请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)。

## <a name="powershell"></a>PowerShell

使用 [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) cmdlet 向 Azure SQL 数据库服务提交导出数据库请求。 根据数据库的大小，导出操作可能需要一些时间才能完成。

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

若要检查导出请求的状态，请使用 [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus) cmdlet。 如果在提交请求后立即运行此命令，通常会返回“状态: 正在进行”。 显示“状态: 成功”时，表示导出完毕。

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>导出 SQL 数据库示例
下面的示例将现有 Azure SQL 数据库导出到 BACPAC，然后说明如何查看导出操作的状态。

若要运行示例，需将几个变量替换为数据库和存储帐户中的特定值。 在 [Azure 门户](https://portal.azure.com)中，浏览到存储帐户，以获取存储帐户名称、blob 容器名称和密钥值。 可单击存储帐户边栏选项卡上的“**访问密钥**”查找密钥。

将以下 `VARIABLE-VALUES` 替换为特定 Azure 资源中的值。 数据库名称就是要导出的现有数据库。

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
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
```

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure SQL 数据库备份的长期备份保留（作为导出数据库进行存档的替代方法），请参阅[长期备份保留](sql-database-long-term-retention.md)。
- 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
* 若要了解如何将 BACPAC 导入到 SQL Server 数据库，请参阅[将 BACPCAC 导入到 SQL Server 数据库](https://msdn.microsoft.com/library/hh710052.aspx)。
* 若要了解如何从 SQL Server 数据库导出 BACPAC，请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)和[迁移你的第一个数据库](sql-database-migrate-your-sql-server-database.md)。

