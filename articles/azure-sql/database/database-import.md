---
title: 导入 BACPAC 文件以在 Azure SQL 数据库中创建数据库
description: 从 BACPAC 文件创建 Azure SQL 数据库或 Azure SQL 托管实例中的新数据库。
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 7bca179f3140a64923af71199fe4a1db48d2065c
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982331"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：将 BACPAC 文件导入到 Azure SQL 数据库或 Azure SQL 中的数据库托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

可以使用[BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac)文件将 SQL Server 数据库导入到 Azure sql 数据库或 SQL 托管实例。 可以从 Azure Blob 存储（仅限标准存储）中存储的 BACPAC 文件或从本地位置中的本地存储导入数据。 若要通过提供更多且更快的资源将导入速度最大化，请在导入过程中将数据库扩展到更高的服务层级和更大的计算大小。 然后，可以在导入成功后进行缩减。

> [!NOTE]
> 导入的数据库的兼容性级别基于源数据库的兼容性级别。

> [!IMPORTANT]
> 导入数据库后，可选择以当前兼容级别（对于 AdventureWorks2008R2 数据库是级别 100）或更高级别操作数据库。 有关在特定兼容级别操作数据库的影响和选项的详细信息，请参阅 [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)（更改数据库兼容级别）。 有关与兼容级别相关的其他数据库级别设置的信息，另请参阅 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)（更改数据库范围的配置）。

## <a name="using-azure-portal"></a>使用 Azure 门户

观看此视频，了解如何从 Azure 门户中的 BACPAC 文件导入，或继续阅读以下内容：

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure 门户](https://portal.azure.com)仅支持在 Azure SQL 数据库中创建单个数据库，并且仅从存储在 Azure Blob 存储中的 BACPAC 文件中创建** **。

若要从 BACPAC 文件将数据库迁移到[AZURE SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)，请使用 SQL Server Management Studio 或 SQLPackage，但目前不支持使用 Azure 门户或 Azure PowerShell。

> [!NOTE]
> 处理通过 Azure 门户或 PowerShell 提交的导入/导出请求的计算机需要存储 BACPAC 文件以及数据层应用程序框架 (DacFX) 生成的临时文件。 相同大小的数据库之间所需的磁盘空间差异很大，所需的磁盘空间最多可能是数据库大小的 3 倍。 运行导入/导出请求的计算机只有 450GB 的本地磁盘空间。 因此，某些请求可能会失败，并显示错误 `There is not enough space on the disk`。 在这种情况下，解决方法是在具有足够本地磁盘空间的计算机上运行 sqlpackage.exe。 我们建议使用 SqlPackage 导入/导出大于 150GB 的数据库以避免此问题。

1. 若要使用 Azure 门户从 BACPAC 文件导入到新的单一数据库中，请打开相应的服务器页，然后在工具栏上选择 "**导入数据库**"。  

   ![数据库 import1](./media/database-import/sql-server-import-database.png)

1. 选择存储帐户和 BACPAC 文件的容器，然后选择要从中导入的 BACPAC 文件。

1. 指定新数据库大小（通常与源数据库相同）并提供目标 SQL Server 凭据。 有关 Azure SQL 数据库中的新数据库可能值的列表，请参阅[Create database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。

   ![数据库 import2](./media/database-import/sql-server-import-database-settings.png)

1. 单击 **“确定”** 。

1. 若要监视导入的进度，请打开数据库的服务器页，然后在“设置”**** 下，选择“导入/导出历史记录”****。 成功导入后，状态为“已完成”****。

   ![数据库导入状态](./media/database-import/sql-server-import-database-history.png)

1. 若要验证数据库在服务器上是否处于活动状态，请选择“SQL 数据库”**** 并验证新数据库是否为“联机”****。

## <a name="using-sqlpackage"></a>使用 SqlPackage

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用程序导入 SQL Server 数据库，请参阅[导入参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) 包括 SqlPackage。 还可以从 Microsoft 下载中心下载最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

在大多数生产环境中，建议使用 SqlPackage 而不是 Azure 门户来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 `BACPAC` 文件进行迁移的博客，请参阅[使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

以下 SqlPackage 命令将**AdventureWorks2008R2**数据库从本地存储导入到名为**MYNEWSERVER20170403)** 的逻辑 SQL 服务器。 它将创建名为 myMigratedDatabase**** 的新数据库，其中包含****“高级”服务层级和 P6**** 服务目标。 根据你的环境更改这些值。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要从企业防火墙之后连接到 Azure SQL 数据库，防火墙必须打开端口1433。 若要连接到 SQL 托管实例，必须具有[点到站点连接](../managed-instance/point-to-site-p2s-configure.md)或 express route 连接。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage 来导入数据库。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>使用 PowerShell

> [!NOTE]
> [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)当前不支持使用 AZURE POWERSHELL 从 BACPAC 文件将数据库迁移到实例数据库中。 若要导入 SQL 托管实例，请使用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 处理通过门户或 Powershell 提交的导入/导出请求的计算机需要存储 bacpac 文件以及数据层应用程序框架 (DacFX) 生成的临时文件。 所需的磁盘空间在具有相同大小的 DB 之间存在显著差异，并且最多可占数据库大小的 3 倍。 运行导入/导出请求的计算机只有 450GB 的本地磁盘空间。 因此，某些请求可能会失败，出现 "磁盘空间不足" 错误。 在这种情况下，解决方法是在具有足够本地磁盘空间的计算机上运行 sqlpackage.exe。 导入/导出大于 150GB 的数据库时，请使用 SqlPackage 来避免此问题。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure 资源管理器（RM）模块仍受支持，但所有将来的开发都适用于 Az .Sql 模块。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

使用[AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet 将导入数据库请求提交到 Azure。 根据数据库大小，导入操作可能需要一些时间才能完成。

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

可以使用 [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet 检查导入的进度。 如果在提交请求后立即运行此 cmdlet，通常会返回 `Status: InProgress`。 当看到 `Status: Succeeded` 时，导入已完成。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用[az-sql-import](/cli/azure/sql/db#az-sql-db-import)命令将导入数据库请求提交到 Azure。 根据数据库大小，导入操作可能需要一些时间才能完成。

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> 有关另一个脚本示例，请参阅[从 BACPAC 文件导入数据库](scripts/import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

- 不支持导入到弹性池中的数据库。 可以将数据导入到单一数据库，然后将数据库移到弹性池。
- 当“允许访问 Azure 服务”设置为“关闭”时，导入/导出服务无法正常工作。 不过，可通过以下方式解决此问题：在 Azure VM 中手动运行 sqlpackage.exe，或者直接在代码中使用 DACFx API 执行导出。

## <a name="import-using-wizards"></a>使用向导导入

还可以使用以下向导。

- [SQL Server Management Studio 中的导入数据层应用程序向导](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 导入和导出向导](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何连接到 Azure SQL 数据库并查询其中的数据库，请参阅[快速入门： AZURE Sql 数据库：使用 SQL Server Management Studio 连接和查询数据](connect-query-ssms.md)。
- 如需 SQL Server 客户顾问团队编写的有关使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](migrate-to-database-from-sql-server.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
