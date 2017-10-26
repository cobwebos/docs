---
title: "用于 SQL 数据库的连接库 | Microsoft 文档"
description: "提供模块的下载链接，这些模块支持使用各种客户端编程语言连接到 SQL Server 和 SQL 数据库。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: genemi
ms.openlocfilehash: 6232877abc1e98b565abee8bf87505fe8edc7791
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>适用于 Microsoft SQL Server 的连接库和框架

请参阅我们的[入门教程](http://aka.ms/sqldev)，快速开始使用 C#、Java、Node.js、PHP 和 Python 等编程语言，并在 Linux 、Windows 或 macOS 上的 Docker 中使用 SQL Server 生成应用程序。

下表列出了客户端应用程序可以使用各种语言连接到的连接库或驱动程序，它们可以用于在 Linux、Windows 或 Docker 上本地或在云中运行的 Microsoft SQL Server，也可以用于 Azure SQL 数据库和 Azure SQL 数据仓库。 

| 语言 | 平台 | 其他资源 | 下载 | 入门 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [下载](https://www.microsoft.com/net/download/) | [入门](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft JDBC 驱动程序](http://msdn.microsoft.com/library/mt484311.aspx) | [下载](https://go.microsoft.com/fwlink/?linkid=852460) |  [入门](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [适用于 SQL Server 的 PHP SQL 驱动程序](http://msdn.microsoft.com/library/dn865013.aspx) | 操作系统： <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [入门](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows、Linux、macOS | [用于 SQL Server 的 Node.js 驱动程序](http://msdn.microsoft.com/library/mt652093.aspx) | [安装](https://msdn.microsoft.com/library/mt652094.aspx) |  [入门](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驱动程序](http://msdn.microsoft.com/library/mt652092.aspx) | 安装选项： <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [入门](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [用于 SQL Server 的 Ruby 驱动程序](http://msdn.microsoft.com/library/mt691981.aspx) | [安装](https://msdn.microsoft.com/library/mt711041.aspx) | [入门](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft ODBC 驱动程序](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [下载](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

下表列出了对象关系映射 (ORM) 框架和 Web 框架的部分示例，客户端应用程序可以将这些框架用于在 Linux、Windows 或 Docker 上本地或在云中运行的 Microsoft SQL Server，也可以用于 Azure SQL 数据库和 Azure SQL 数据仓库。 

| 语言 | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [实体框架](https://docs.microsoft.com/en-us/ef)<br>[实体框架核心](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows、Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](http://rubyonrails.org/) |

## <a name="related-links"></a>相关链接
- 用于从客户端应用程序建立连接的 [SQL Server 驱动程序](http://msdn.microsoft.com/library/mt654049.aspx)
- [使用 .NET (C#) 连接到 SQL 数据库](sql-database-connect-query-dotnet.md)
- [使用 PHP 连接到 SQL 数据库](sql-database-connect-query-php.md)
- [使用 Node.js 连接到 SQL 数据库](sql-database-connect-query-nodejs.md)
- [使用 Java 连接到 SQL 数据库](sql-database-connect-query-java.md)
- [使用 Python 连接到 SQL 数据库](sql-database-connect-query-python.md)
- [使用 Ruby 连接到 SQL 数据库](sql-database-connect-query-ruby.md)
