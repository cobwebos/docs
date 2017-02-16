---
title: "SQLPackage：Azure SQL Server 数据库兼容性 | Microsoft Docs"
description: "本文中将介绍如何使用 SqlPackage 确定要迁移到 SQL 数据库的 SQL Server 数据库是否兼容"
keywords: "Microsoft Azure SQL 数据库, 数据库迁移, SQL 数据库兼容性, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6728693290e2c1d6c970c60c7d8ef674764e053c
ms.openlocfilehash: 834dae604fa87f753e8d050ce7cdfa7e9613f462


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>使用 SqlPackage.exe 确定 SQL 数据库兼容性
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

在本文中，你将了解如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示实用程序确定要迁移到 SQL 数据库的 SQL Server 数据库是否兼容。

## <a name="using-sqlpackageexe"></a>使用 SqlPackage.exe
1. 打开命令提示符并更改包含 sqlpackage.exe 最新版本的目录。 此实用程序附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[适用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876)。

2. 使用你的环境的以下参数运行以下 SqlPackage 命令：

   ```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
   ```   

   | 参数 | 说明 |
   | --- | --- |
   | < server_name > |源服务器名称 |
   | < database_name > |源数据库名称 |
   | < target_file > |BACPAC 文件的文件名和位置 |
   | < schema_name.table_name > |其数据将输出到目标文件的表 |
   | < output_file > |包含错误（如果有）的输出文件的文件名和位置 |
   
    使用 /P:TableData 参数的原因是：只需测试导出到 Azure SQL DB V12 的数据库兼容性，无需导出所有表中的数据。 遗憾的是，sqlpackage.exe 的导出参数不支持提取零个表。 需指定至少一个表，例如一个小型表。 < output_file > 包含任何错误的报告。 “2>&1”字符串将执行命令后生成的标准输出和标准错误传送到指定的输出文件。
   
    ![通过“任务”菜单导出数据层应用程序](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 打开输出文件并查看兼容性错误（如果有）。 
   
    ![通过“任务”菜单导出数据层应用程序](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>后续步骤
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [修复数据库迁移的兼容性问题](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [将兼容的 SQL Server 数据库迁移到 SQL 数据库](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他资源
* [SQL 数据库功能](sql-database-features.md)
* [Transact-SQL 部分支持或不支持的函数](sql-database-transact-sql-information.md)
* [使用 SQL Server 迁移助手迁移非 SQL Server 数据库](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO2-->


