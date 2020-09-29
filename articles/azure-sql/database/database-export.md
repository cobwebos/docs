---
title: 将 Azure SQL 数据库导出到 BACPAC 文件（Azure 门户）
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 使用 Azure 门户将数据库导出到 BACPAC 文件。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.topic: how-to
ms.openlocfilehash: b91b7175fa4c7b91fec63a817206fa540813bdb7
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443800"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>导出到 BACPAC 文件 - Azure SQL 数据库和 Azure SQL 托管实例

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

如果需要为存档或移动到其他平台而导出数据库，可将数据库架构和数据导出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件。 BACPAC 文件是一个扩展名为 BACPAC 的 ZIP 文件，它包含来自数据库的元数据和数据。 可将 BACPAC 文件存储在 Azure Blob 存储中或本地位置的本地存储中，之后可导入回 Azure SQL 数据库、Azure SQL 托管实例或 SQL Server 实例。

## <a name="considerations"></a>注意事项

- 为保证导出的事务处理方式一致，必须确保导出期间未发生写入活动，或者正在从数据库的[事务处理方式一致性副本](database-copy.md)中导出。
- 如果是导出到 Blob 存储，则 BACPAC 文件的最大大小为 200 GB。 若要存档更大的 BACPAC 文件，请导出到本地存储。
- 不支持使用本文所述方法将 BACPAC 文件导出到 Azure 高级存储。
- 目前不支持有防火墙的存储。
- 如果导出操作超过 20 个小时，可能会取消操作。 为提高导出过程中的性能，可以进行如下操作：

  - 暂时提高计算大小。
  - 在导出期间终止所有读取和写入活动。
  - 对所有大型表上的非 null 值使用[聚集索引](https://msdn.microsoft.com/library/ms190457.aspx)。 如果不使用聚集索引，当时间超过 6-12 个小时时，导出可能会失败。 这是因为导出服务需要完成表格扫描，才能尝试导出整个表格。 确认表是否针对导出进行优化的一个好方法是，运行 **DBCC SHOW_STATISTICS** 并确保 *RANGE_HI_KEY* 不是 null 并且值分布良好。 有关详细信息，请参阅 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 不能用于备份和还原操作。 Azure 会自动为每个用户数据库创建备份。 有关详细信息，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)和 [SQL 数据库备份](automated-backups-overview.md)。

## <a name="the-azure-portal"></a>Azure 门户

目前不支持使用 Azure 门户从 [Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)导出数据库的 BACPAC。 请改用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 处理通过 Azure 门户或 PowerShell 提交的导入/导出请求的计算机需要存储 BACPAC 文件以及数据层应用程序框架 (DacFX) 生成的临时文件。 相同大小的数据库之间所需的磁盘空间差异很大，所需的磁盘空间最多可能是数据库大小的 3 倍。 运行导入/导出请求的计算机只有 450 GB 的本地磁盘空间。 因此，某些请求可能会失败，并显示错误 `There is not enough space on the disk`。 在这种情况下，解决方法是在具有足够本地磁盘空间的计算机上运行 sqlpackage.exe。 我们建议使用 [SqlPackage](#sqlpackage-utility) 导入/导出大于 150GB 的数据库以避免此问题。

1. 若要使用 [Azure 门户](https://portal.azure.com)导出数据库，请打开数据库页，并在工具栏上单击“导出”。

   ![数据库导出](./media/database-export/database-export1.png)

2. 指定 BACPAC 文件名，为导出选择现有的 Azure 存储帐户和容器，然后提供用于访问源数据库的相应凭据。 即使你是 Azure 管理员，此处也需要 SQL Server 管理员登录名，因为是 Azure 管理员并不等同于在 Azure SQL 数据库或 Azure SQL 托管实例中拥有管理员权限。

    ![数据库导出](./media/database-export/database-export2.png)

3. 单击 **“确定”** 。

4. 若要监视导出操作的进度，请打开包含所导出数据库的服务器的相应页面。 向下滚动到“设置”，并单击“导入/导出历史记录” 。

   ![导出历史记录](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage 实用程序

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用工具导出 SQL 数据库中的数据库，请参阅[导出参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 可以使用 SQLPackage 实用工具附带的最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

在大多数生产环境中，建议使用 SQLPackage 实用工具来实现缩放和性能。 如需 SQL Server 客户顾问团队编写的有关使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage.exe 来导出数据库：

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

在 SQL Server Management Studio 的最新版本中，有向导来指示如何将 Azure SQL 数据库或 SQL 托管实例数据库中的数据库导出到 BACPAC 文件。 请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)。

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)当前不支持使用 Azure PowerShell 将数据库导出到 BACPAC 文件。 若要将托管实例导出到 BACPAC 文件，请使用 SQL Server Management Studio 或 SQLPackage。

使用 [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet 向 Azure SQL 数据库服务提交导出数据库请求。 根据数据库的大小，导出操作可能需要一些时间才能完成。

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

若要检查导出请求的状态，请使用 [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet。 如果在提交请求后立即运行此命令，通常会返回“状态: 正在进行”。 显示“状态: 成功”时，表示导出完毕。

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>后续步骤

- 若要了解单一数据库和共用数据库的长期备份保留（作为导出数据库进行存档的替代方法），请参阅[长期备份保留](long-term-retention-overview.md)。 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为长期备份保留的替代方案。
- 如需 SQL Server 客户顾问团队编写的有关使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 要了解如何将 BACPAC 导入到 SQL Server 数据库，请参阅[将 BACPAC 导入 SQL Server 数据库](https://msdn.microsoft.com/library/hh710052.aspx)。
- 若要了解如何从 SQL Server 数据库导出 BACPAC，请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- 若要了解如何使用数据迁移服务迁移数据库，请参阅[使用 DMS 从 SQL Server 脱机迁移到 Azure SQL 数据库](../../dms/tutorial-sql-server-to-azure-sql.md)。
- 如果要从 SQL Server 中导出作为迁移到 Azure SQL 数据库的准备，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](migrate-to-database-from-sql-server.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
