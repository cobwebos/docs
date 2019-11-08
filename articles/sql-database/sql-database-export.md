---
title: 将单个或共用数据库导出到 BACPAC 文件
description: 使用 Azure 门户将 Azure SQL 数据库导出到 BACPAC 文件
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b96d2e69b757774afe177341caffe2f3cbf00e2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823456"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>将 Azure SQL 数据库导出到 BACPAC 文件

如果需要为存档或移动到其他平台而导出数据库，可将数据库架构和数据导出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件。 BACPAC 文件是扩展名为 BACPAC 的 ZIP 文件，其中包含来自 SQL Server 数据库的元数据和数据。 可将 BACPAC 文件存储在 Azure Blob 存储中或存储在本地位置的本地存储中，之后可以导入回 Azure SQL 数据库或 SQL Server 本地安装。

## <a name="considerations-when-exporting-an-azure-sql-database"></a>导出 Azure SQL 数据库时的注意事项

- 为保证导出的事务处理方式一致，须确保导出期间未发生写入活动，或者正在从 Azure SQL 数据库的[事务处理方式一致性副本](sql-database-copy.md)中导出。
- 如果计划导出到 blob 存储，则 BACPAC 文件的最大大小为 200 GB。 若要存档更大的 BACPAC 文件，请导出到本地存储。
- 本文介绍的方法不支持将 BACPAC 文件导出到 Azure 高级存储。
- 目前不支持有防火墙的存储。
- 如果从 Azure SQL 数据库的导出操作超过 20 个小时，则可能会取消操作。 为提高导出过程中的性能，可以进行如下操作：

  - 暂时提高计算大小。
  - 在导出期间终止所有读取和写入活动。
  - 对所有大型表格上的非 null 值使用[聚集索引](https://msdn.microsoft.com/library/ms190457.aspx)。 如果不使用聚集索引，当时间超过 6-12 个小时时，导出可能会失败。 这是因为导出服务需要完成表格扫描，才能尝试导出整个表格。 确认表格是否针对导出进行优化的一个好方法是，运行 **DBCC SHOW_STATISTICS** 并确保 *RANGE_HI_KEY* 不是 null 并且值分布良好。 相关详细信息，请参阅 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 不能用于备份和还原操作。 Azure SQL 数据库会自动为每个用户数据库创建备份。 有关详细信息，请参阅[业务连续性概述](sql-database-business-continuity.md)和 [SQL 数据库备份](sql-database-automated-backups.md)。

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>使用 Azure 门户导出到 BACPAC 文件

目前不支持使用 Azure PowerShell 从[托管实例](sql-database-managed-instance.md)导出数据库的 BACPAC。 请改用 SQL Server Management Studio 或 SQLPackage。

> [!NOTE]
> 处理通过 Azure 门户或 PowerShell 提交的导入/导出请求的计算机需要存储 BACPAC 文件以及数据层应用程序框架 (DacFX) 生成的临时文件。 相同大小的数据库之间所需的磁盘空间差异很大，所需的磁盘空间最多可能是数据库大小的 3 倍。 运行导入/导出请求的计算机只有 450GB 的本地磁盘空间。 因此，某些请求可能会失败，并显示错误 `There is not enough space on the disk`。 在这种情况下，解决方法是在具有足够本地磁盘空间的计算机上运行 sqlpackage.exe。 我们建议使用 [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) 导入/导出大于 150GB 的数据库以避免此问题。

1. 若要使用 [Azure 门户](https://portal.azure.com)导出数据库，请打开数据库页，并在工具栏上单击“导出”。

   ![数据库导出](./media/sql-database-export/database-export1.png)

2. 指定 BACPAC 文件名，为导出选择现有的 Azure 存储帐户和容器，然后提供用于访问源数据库的相应凭据。 即使你是 Azure 管理员，此处也需要 SQL Server 管理员登录名，因为作为 Azure 管理员并不等同于拥有 SQL Server 管理员权限。

    ![数据库导出](./media/sql-database-export/database-export2.png)

3. 单击 **“确定”** 。

4. 若要监视导出操作的进度，请打开包含待导出数据库的 SQL 数据库服务器的相应页面。 向下滚动到“设置”，并单击“导入/导出历史记录”。

   ![导出历史记录](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>使用 SQLPackage 实用工具导出到 BACPAC 文件

若要使用 [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 命令行实用工具导出 SQL 数据库，请参阅[导出参数和属性](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)。 SQLPackage 实用工具附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

在大多数生产环境中，建议使用 SQLPackage 实用工具来实现缩放和性能。 有关 SQL Server 客户咨询团队介绍如何使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。

此示例演示如何通过 Active Directory 通用身份验证，使用 SqlPackage.exe 来导出数据库：

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 导出到 BACPAC 文件

SQL Server Management Studio 的最新版本提供一个向导，可将 Azure SQL 数据库导出到 BACPAC 文件。 请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)。

## <a name="export-to-a-bacpac-file-using-powershell"></a>使用 PowerShell 导出到 BACPAC 文件

> [!NOTE]
> [托管实例](sql-database-managed-instance.md)当前不支持使用 Azure PowerShell 将数据库导出到 BACPAC 文件。 若要将托管实例导出到 BACPAC 文件，请使用 SQL Server Management Studio 或 SQLPackage。

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

- 若要了解单一数据库和共用数据库的长期备份保留（作为导出数据库进行存档的替代方法），请参阅[长期备份保留](sql-database-long-term-retention.md)。 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为长期备份保留的替代方案。
- 有关 SQL Server 客户咨询团队介绍如何使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
- 要了解如何将 BACPAC 导入到 SQL Server 数据库，请参阅[将 BACPAC 导入 SQL Server 数据库](https://msdn.microsoft.com/library/hh710052.aspx)。
- 若要了解如何从 SQL Server 数据库导出 BACPAC，请参阅[导出数据层应用程序](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- 若要了解如何使用数据迁移服务迁移数据库，请参阅[使用 DMS 将 SQL Server 脱机迁移到 Azure SQL 数据库](../dms/tutorial-sql-server-to-azure-sql.md)。
- 如果将从 SQL Server 中导出作为迁移到 Azure SQL 数据库的准备，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-single-database-migrate.md)。
- 若要了解如何安全地管理和共享存储密钥和共享访问签名，请参阅 [Azure 存储安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。
