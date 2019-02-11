---
title: 导入 BACPAC 文件以创建 Azure SQL 数据库 | Microsoft 文档
description: 通过导入 BACPAC 文件创建一个新的 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: douglaslMS
ms.author: douglasl
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c1b6c55475c1600c89c1ac1cae9dee0068b92070
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478213"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>快速入门：将 BACPAC 文件导入到新的 Azure SQL 数据库

可以使用 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件（扩展名为 `.bacpac` 且包含数据库的元数据和数据的 zip 文件）将 SQL Server 数据库迁移到 Azure SQL 数据库。 可以从 Azure blob 存储（仅限标准存储）或从本地位置中的本地存储导入 BACPAC 文件。 若要最大限度地提高导入速度，可以指定较高的服务层和计算大小（例如 P6）。 然后，可以在导入成功后减少其值。 导入的数据库的兼容性级别基于源数据库的兼容性级别。

> [!IMPORTANT]
> 导入数据库后，可选择以当前兼容级别（对于 AdventureWorks2008R2 数据库是级别 100）或更高级别操作数据库。 有关在特定兼容级别操作数据库的影响和选项的详细信息，请参阅 [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)（更改数据库兼容级别）。 有关与兼容级别相关的其他数据库级别设置的信息，另请参阅 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)（更改数据库范围的配置）。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>在 Azure 门户中从 BACPAC 文件导入

本部分介绍如何在 [Azure 门户](https://portal.azure.com)中根据存于 Azure blob 存储中的 BACPAC 文件创建 Azure SQL 数据库。 该门户仅支持从 Azure blob 存储导入 BACPAC 文件。

> [!NOTE]
> [Azure SQL 数据库托管实例](sql-database-managed-instance.md)支持使用本文中的其他方法从 BACPAC 文件导入，但目前不支持在 Azure 门户中迁移。

若要在 Azure 门户中导入数据库，请打开将托管导入的 SQL 数据库服务器对应的页面，然后在工具栏上选择“导入数据库”。  

   ![数据库导入](./media/sql-database-import/import.png)

选择存储帐户、容器，以及要导入的 BACPAC 文件。 指定新数据库大小（通常与源数据库相同）并提供目标 SQL Server 凭据。 

### <a name="monitor-imports-progress"></a>监视导入的进度

若要监视导入的进度，请打开导入的数据库的服务器页，然后在“设置”下，选择“导入/导出历史记录”。 成功导入后，状态为“已完成”。

若要验证数据库在服务器上是否处于活动状态，请选择“SQL 数据库”并验证新数据库是否为“联机”。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 从 BACPAC 文件导入

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用工具导入 SQL 数据库，请参阅[导入参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)。 SqlPackage 包含最新的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)。 还可以从 Microsoft 下载中心下载最新的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

在大多数生产环境中，建议使用 SqlPackage 来实现缩放和性能。 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。

以下 SqlPackage 命令可将 AdventureWorks2008R2 数据库从本地存储导入到名为 mynewserver20170403 的 Azure SQL 数据库服务器。 它将创建名为 myMigratedDatabase 的新数据库，其中包含高级服务层和 P6 服务目标。 根据你的环境更改这些值。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> SQL 数据库服务器在端口 1433 上进行侦听。 若要从公司防火墙后连接到 SQL 数据库服务器，该防火墙必须打开此端口。
>

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage 来导入数据库。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>使用 PowerShell 从 BACPAC 文件导入

使用 [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet 向 Azure SQL 数据库服务提交导入数据库请求。 根据数据库大小，导入操作可能需要一些时间才能完成。

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 可以使用 [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet 检查导入的进度。 如果在提交请求后立即运行此 cmdlet，通常会返回“状态: 正在进行”。 显示“状态: 成功”时，表示导入完毕。

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
有关另一个脚本示例，请参阅[从 BACPAC 文件导入数据库](scripts/sql-database-import-from-bacpac-powershell.md)。

## <a name="limitations"></a>限制

不支持导入到弹性池中的数据库。 可以将数据导入到单一数据库，然后将数据库移到弹性池。

## <a name="import-using-wizards"></a>使用向导导入

还可以使用以下向导。

- [SQL Server Management Studio 中的“导入数据层应用程序”向导](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server 导入和导出向导](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何连接到导入的 SQL 数据库并进行查询，请参阅[快速入门：Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询](sql-database-connect-query-ssms.md)。
- 有关 SQL Server 客户咨询团队使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
