---
title: "SqlPackage：从 BACPAC 文件导入到 Azure SQL 数据库 | Microsoft Docs"
description: "本文说明如何使用 SqlPackage 命令行实用程序从 BACPAC 文件导入到 SQL 数据库。"
keywords: "Microsoft Azure SQL 数据库, 数据库迁移, 导入数据库, 导入 BACPAC 文件, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>使用 SqlPackage 将数据库从 BACPAC 文件导入到 Azure SQL 数据库

本文说明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令行实用程序从 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件导入到 SQL 数据库。 此实用程序附带最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和[适用于 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)，也可以直接从 Microsoft 下载中心下载最新版本的 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。

> [!NOTE]
> 以下步骤假定用户已预配 SQL 数据库服务器，手头有连接信息，并且已验证源数据库兼容。
> 
> 

## <a name="import-the-database"></a>导入数据库
使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令行实用程序从 BACPAC 文件导入兼容的 SQL Server 数据库（或 Azure SQL 数据库）。

> [!NOTE]
> 以下步骤假定用户已预配 Azure SQL 数据库服务器并且手头有连接信息。
>  

在包含最新版 sqlpackage.exe 命令行实用程序的目录的命令提示符处，执行类似于以下示例命令的命令，将 BACPAC 文件导入 Azure SQL 数据库用作 Premium P11。

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>后续步骤

* 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
* 有关 SQLPackage 上的参考内容，请参阅 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)。
* 若要下载最新版本的 SQLPackage，请参阅 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)。
* 有关 SQL Server 客户咨询团队介绍如何使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。




<!--HONumber=Feb17_HO2-->


