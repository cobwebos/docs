---
title: "Azure Database for PostgreSQL 的连接库 | Microsoft Docs"
description: "本文介绍了几个库和驱动程序，开发人员可在对应用程序编码以连接和查询 PostgreSQL 的 Azure 数据库时，使用这些库和驱动程序。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/15/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7099e26fd749820ab6869c0262e1f97d993ab7b2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 的连接库
本主题列出了几个库和驱动程序，开发人员可在对应用程序编程以连接和查询 PostgreSQL 的 Azure 数据库时，使用这些库和驱动程序。

## <a name="client-interfaces"></a>客户端接口
要连接到 PostgreSQL 服务器的大多数语言客户端库为外部项目，并且独立分发。 Windows、Linux 和 Mac 平台支持这些客户端库。 下面列出了一些常用的客户端驱动程序：

| 语言 | 客户端接口 | 其他信息 | **下载** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | 符合 DB API 2.0 规范 | [下载](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | 数据库扩展 | [安装](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm 包](https://www.npmjs.com/package/pg) | 纯 JavaScript 非阻止客户端 | [安装](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | 类型 4 JDBC 驱动程序 | [下载](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby 接口 | [下载](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 纯 Go 语言 postgres 驱动程序 | [安装](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | ADO.NET 数据提供程序 | [下载](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC 驱动程序 | [下载](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | 主要 C 语言接口 | 附送 |
| C++ | [libpqxx](http://pqxx.org/) | 新样式 C++ 接口 | [下载](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>后续步骤
使用所选语言阅读这些快速入门，了解如何连接并查询用于 PostgreSQL 的 Azure 数据库：

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [.NET (C#)](./connect-csharp.md)

