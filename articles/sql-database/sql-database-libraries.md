---
title: 用于 SQL 数据库的连接库 | Microsoft 文档
description: 提供模块的下载链接，这些模块支持使用各种客户端编程语言连接到 SQL Server 和 SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 874aa2610ae1785e9f40841ed3c3279aac42d660
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321817"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>适用于 SQL Server 的连接库和框架

查看[入门教程](https://aka.ms/sqldev)，快速开始使用 C#、Java、Node.js、PHP 和 Python 等编程语言。 然后使用 Linux、Windows 或 Docker（对于 macOS）上的 SQL Server 生成应用。

下表列出的连接库或驱动程序可供客户端应用程序使用各种语言进行连接并使用在本地或云中运行的 SQL Server。 可以在 Linux、Windows 或 Docker 上使用它们，并将其用于连接到 Azure SQL 数据库和 Azure SQL 数据仓库。 

| 语言 | 平台 | 其他资源 | 下载 | 入门 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [下载](https://www.microsoft.com/net/download/) | [入门](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [Microsoft JDBC driver for SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [下载](https://go.microsoft.com/fwlink/?linkid=852460) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [用于 SQL Server 的 PHP SQL 驱动程序](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [下载](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [入门](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [用于 SQL Server 的 Node.js 驱动程序](https://msdn.microsoft.com/library/mt652093.aspx) | [安装](https://msdn.microsoft.com/library/mt652094.aspx) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驱动程序](https://msdn.microsoft.com/library/mt652092.aspx) | 安装选项： <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [用于 SQL Server 的 Ruby 驱动程序](https://msdn.microsoft.com/library/mt691981.aspx) | [安装](https://msdn.microsoft.com/library/mt711041.aspx) | [入门](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [下载](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

下表列出了客户端应用程序可以将其与在本地或云中运行的 SQL Server 一起使用的对象关系映射 (ORM) 框架和 Web 框架的示例。 可以在 Linux、Windows 或 Docker 上使用这些框架，并将其用于连接到 SQL 数据库和 Azure SQL 数据仓库。 

| 语言 | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [实体框架](https://docs.microsoft.com/ef)<br>[实体框架核心](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows、Linux、macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="related-links"></a>相关链接
- 用于从客户端应用程序建立连接的 [SQL Server 驱动程序](https://msdn.microsoft.com/library/mt654049.aspx)
- 连接到 SQL 数据库：
    - [使用 .NET (C#) 连接到 SQL 数据库](sql-database-connect-query-dotnet.md)
    - [使用 PHP 连接到 SQL 数据库](sql-database-connect-query-php.md)
    - [使用 Node.js 连接到 SQL 数据库](sql-database-connect-query-nodejs.md)
    - [使用 Java 连接到 SQL 数据库](sql-database-connect-query-java.md)
    - [使用 Python 连接到 SQL 数据库](sql-database-connect-query-python.md)
    - [使用 Ruby 连接到 SQL 数据库](sql-database-connect-query-ruby.md)
- 重试逻辑代码示例：
    - [使用 ADO.NET 弹性连接到 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [使用 PHP 弹性连接到 SQL][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

