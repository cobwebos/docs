---
title: 导入 BACPAC 文件以创建 Azure SQL 数据库 | Microsoft 文档
description: 通过导入 BACPAC 文件创建一个新的 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 5e0f3ccafa449cc431166d5b4bd4135a1d1ac97c
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675881"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>快速入门：将 BACPAC 文件导入到 Azure SQL 数据库中的数据库

可以使用 [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 文件将 SQL Server 数据库导入 Azure SQL 数据库中的数据库。 可以从 Azure Blob 存储（仅限标准存储）中存储的 `BACPAC` 文件或从本地位置中的本地存储导入数据。 若要通过提供更多且更快的资源将导入速度最大化，请在导入过程中将数据库扩展到更高的服务层级和更大的计算大小。 然后，可以在导入成功后减少其值。

> [!NOTE]
> 导入的数据库的兼容性级别基于源数据库的兼容性级别。
> [!IMPORTANT]
> 导入数据库后，可选择以当前兼容级别（对于 AdventureWorks2008R2 数据库是级别 100）或更高级别操作数据库。 有关在特定兼容级别操作数据库的影响和选项的详细信息，请参阅 [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)（更改数据库兼容级别）。 另请参阅 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)，了解与兼容性级别相关的其他数据库级别设置。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>在 Azure 门户中从 BACPAC 文件导入

[Azure 门户](https://portal.azure.com)仅支持在 Azure SQL 数据库中创建单个数据库，并且仅从存储在 Azure Blob 存储中的 BACPAC 文件中创建。

当前不支持使用 Azure PowerShell 从 BACPAC 文件将数据库迁移到[托管实例](sql-database-managed-instance.md)。 请改用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 处理通过 Azure 门户或 PowerShell 提交的导入/导出请求的计算机需要存储 BACPAC 文件以及由数据层应用程序框架（DacFX）生成的临时文件。 所需的磁盘空间在大小相同的数据库之间发生了重大变化，并且需要磁盘空间的大小最多可达数据库大小的3倍。 运行导入/导出请求的计算机仅具有450GB 的本地磁盘空间。 因此，某些请求可能会失败，并 `There is not enough space on the disk` 错误。 在这种情况下，解决方法是在具有足够的本地磁盘空间的计算机上运行 sqlpackage。 我们鼓励使用[SqlPackage](#import-from-a-bacpac-file-using-sqlpackage)导入/导出大于 150 gb 的数据库，以避免此问题。
 
1. 若要使用 Azure 门户从 BACPAC 文件导入新的单个数据库，请打开相应的数据库服务器页面，然后在工具栏上选择“导入数据库”。  

   ![数据库 import1](./media/sql-database-import/import1.png)

2. 选择存储帐户和 BACPAC 文件的容器，然后选择要从中导入的 BACPAC 文件。
3. 指定新数据库大小（通常与源数据库相同）并提供目标 SQL Server 凭据。 如需新 Azure SQL 数据库的可能值的列表，请参阅[创建数据库](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。

   ![数据库 import2](./media/sql-database-import/import2.png)

4. 单击 **“确定”** 。

5. 若要监视导入的进度，请打开数据库的服务器页，然后在“设置”下，选择“导入/导出历史记录”。 成功导入后，状态为“已完成”。

   ![数据库导入状态](./media/sql-database-import/import-status.png)

6. 若要验证数据库在数据库服务器上是否处于活动状态，请选择“SQL 数据库”并验证新数据库是否为“联机”。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 从 BACPAC 文件导入

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用程序导入 SQL Server 数据库，请参阅[导入参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 包含最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)。 还可以从 Microsoft 下载中心下载最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

在大多数生产环境中，建议使用 SqlPackage 而不是 Azure 门户来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 `BACPAC` 文件进行迁移的博客，请参阅[使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

以下 SqlPackage 命令可将 AdventureWorks2008R2 数据库从本地存储导入到名为 mynewserver20170403 的 Azure SQL 数据库服务器。 它将创建名为 myMigratedDatabase 的新数据库，其中包含“高级”服务层级和 P6 服务目标。 根据你的环境更改这些值。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 若要从公司防火墙后连接到管理单一数据库的 SQL 数据库服务器，该防火墙必须打开端口 1433。 若要连接到托管实例，必须有[点到站点连接](sql-database-managed-instance-configure-p2s.md)或快速路由连接。
>

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage 来导入数据库。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>使用 PowerShell 从 BACPAC 文件导入单个数据库

> [!NOTE]
> [托管实例](sql-database-managed-instance.md)当前不支持使用 AZURE POWERSHELL 从 BACPAC 文件将数据库迁移到实例数据库中。 若要导入托管实例，请使用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 处理通过门户或 Powershell 提交的导入/导出请求的计算机需要存储 bacpac 文件以及由数据层应用程序框架（DacFX）生成的临时文件。 所需的磁盘空间在大小相同的数据库之间发生了重大变化，并且最多可能需要3倍的数据库大小。 运行导入/导出请求的计算机仅具有450GB 的本地磁盘空间。 因此，某些请求可能会失败，出现 "磁盘空间不足" 错误。 在这种情况下，解决方法是在具有足够的本地磁盘空间的计算机上运行 sqlpackage。 导入/导出大于 150 GB 的数据库时，请使用[SqlPackage](#import-from-a-bacpac-file-using-sqlpackage)来避免此问题。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器模块，但所有将来的开发都适用于 Az .Sql 模块。 有关这些 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令的参数完全相同。

使用[AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) Cmdlet 向 Azure SQL 数据库服务提交导入数据库请求。 根据数据库大小，导入操作可能需要一些时间才能完成。

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 可以使用[AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet 检查导入的进度。 在请求后立即运行 cmdlet 通常返回**状态：** 正在进行。 当你看到 "**状态：成功**" 时，导入完成。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> 有关另一个脚本示例，请参阅[从 BACPAC 文件导入数据库](scripts/sql-database-import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

不支持导入到弹性池中的数据库。 可以将数据导入到单一数据库，然后将数据库移到弹性池。

## <a name="import-using-wizards"></a>使用向导导入

还可以使用以下向导。

- [SQL Server Management Studio 中的“导入数据层应用程序”向导](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 导入和导出向导](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何连接并查询导入的 SQL 数据库，请参阅[快速入门： AZURE SQL 数据库：使用 SQL Server Management Studio 来连接和查询数据](sql-database-connect-query-ssms.md)。
- 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-single-database-migrate.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
