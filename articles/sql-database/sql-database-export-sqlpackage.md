---
title: "SqlPackage：将 SQL Server 数据库导出到 BACPAC 文件 (Azure) | Microsoft Docs"
description: "本文说明如何使用 SqlPackage 命令行实用程序将 SQL Server 数据库导出到 BACPAC 文件。"
keywords: "Microsoft Azure SQL 数据库, 数据库迁移, 导出数据库, 导出 BACPAC 文件, sqlpackage"
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
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: a17bfa6868ac581dc5ae562b3174f28571d52698


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>使用 SqlPackage 将 Azure SQL 数据库或 SQL Server 数据库导出到 BACPAC 文件

本文说明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令行实用程序将 Azure SQL 数据库或 SQL Server 数据库导出到 BACPAC 文件。 此实用程序附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[适用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

有关导出到 BACPAC 文件的概述，请参阅[导出到 BACPAC](sql-database-export.md)。

> [!NOTE]
> 还可使用 [Azure 门户](sql-database-export-portal.md)、[SQL Server Management Studio](sql-database-export-ssms.md) 或 [PowerShell](sql-database-export-powershell.md) 将 Azure SQL 数据库文件导出到 BACPAC 文件。
>

## <a name="sqlpackage-utility"></a>SQLPackage 实用工具

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

* To learn about importing a BACPAC using SQLPackage, see [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)
* To learn about importing a BACPAC using the Azure portal, see [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)
* To learn about importing a BACPAC using PowerShell, see [Import a BACPAC to Azure SQL Database using PowerShell](sql-database-import-powershell.md)
* For a discussion of the entire SQL Server database migration process, including performance recommendations, see [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md).
* To learn about long-term backup retention of an Azure SQL database backup as an alternative to exported a database for archive purposes, see [Long term backup retention](sql-database-long-term-retention.md)
* To learn about importing a BACPAC to a SQL Server database, see [Import a BACPCAC to a SQL Server database](https://msdn.microsoft.com/library/hh710052.aspx)





<!--HONumber=Feb17_HO2-->


