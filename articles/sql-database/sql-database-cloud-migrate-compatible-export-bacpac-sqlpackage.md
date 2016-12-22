---
title: "使用 SqlPackage 将 SQL Server 数据库导出到 BACPAC 文件 | Microsoft 文档"
description: "Microsoft Azure SQL 数据库, 数据库迁移, 导出数据库, 导出 BACPAC 文件, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 47c4c2e78432746a0a03f98692ddcd4602637212


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 将 SQL Server 数据库导出到 BACPAC 文件
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

本文说明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令行实用程序将 SQL Server 数据库导出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件。 此实用程序附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[适用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

1. 打开命令提示符并更改包含 sqlpackage.exe 命令行实用程序的目录 - 此实用程序与 Visual Studio 和 SQL Server 一起提供。 使用计算机上的搜索来查找你环境中的路径。
2. 结合环境的以下参数运行以下 sqlpackage.exe 命令：
   
```    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps
* [Newest version of SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Newest version of SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Import a BACPAC to Azure SQL Database using SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Import a BACPAC to Azure SQL Database SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Import a BACPAC to Azure SQL Database Azure portal](sql-database-import.md)
* [Import a BACPAC to Azure SQL Database PowerShell](sql-database-import-powershell.md)

## Additional resources
* [SQL Database features](sql-database-features.md)
* [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


