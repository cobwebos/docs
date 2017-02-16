---
title: "SAMW：修复 Azure SQL 数据库兼容性迁移问题 | Microsoft Docs"
description: "在本文中，你将了解在迁移到 Azure SQL 数据库之前如何使用 SQL Azure 迁移向导检测并解决 SQL Server 数据库的兼容性问题。"
keywords: "Microsoft Azure SQL 数据库, 数据库迁移, 兼容性, SQL Azure 迁移向导"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: bba0893a-3247-406c-b155-acb4b3ef479c
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 006d2d5cd24bc42655bf0b3c0325531144743524


---
# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>在迁移到 Azure SQL 数据库之前使用 SQL Azure 迁移向导解决 SQL Server 数据库兼容性问题
> [!div class="op_single_selector"]
> * 使用 [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * 使用 [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * 使用 [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

在本文中，你将了解在迁移到 Azure SQL 数据库之前如何使用 SQL Azure 迁移向导检测并解决 SQL Server 数据库的兼容性问题。

## <a name="using-sql-azure-migration-wizard"></a>使用 SQL Azure 迁移向导
使用 [SQL Azure 迁移向导](http://sqlazuremw.codeplex.com/) CodePlex 工具，从不兼容的源数据库生成 T-SQL 脚本。 然后，向导会对此脚本进行转换，使之与 SQL 数据库兼容。 然后，用户即可连接到 Azure SQL 数据库执行此脚本。 此工具还可分析跟踪文件，确定兼容性问题。 生成的脚本可以只包含架构，也可以包含 BCP 格式的数据。 其他文档（包括分步指南）可在 CodePlex 上的 [SQL Azure 迁移向导](http://sqlazuremw.codeplex.com/)中找到。  

 ![SAMW 迁移示意图](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> 向导的内置转换并非能够修复它可检测到的所有不兼容的架构。 无法解决的非兼容脚本将报告为错误，将在生成的脚本中注入注释。 如果检测到许多错误，请使用 Visual Studio 或 SQL Server Management Studio 来单步执行并修复无法使用 SQL Server 迁移向导修复的每个错误。
> 
> 

## <a name="next-steps"></a>后续步骤
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [将兼容的 SQL Server 数据库迁移到 SQL 数据库](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他资源
* [SQL 数据库功能](sql-database-features.md)
* [Transact-SQL 部分支持或不支持的函数](sql-database-transact-sql-information.md)
* [使用 SQL Server 迁移助手迁移非 SQL Server 数据库](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


