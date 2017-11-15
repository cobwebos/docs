---
title: "Azure Database for PostgreSQL 的连接库 | Microsoft Docs"
description: "本文介绍了几个库和驱动程序，开发人员可在对应用程序编码以连接和查询 PostgreSQL 的 Azure 数据库时，使用这些库和驱动程序。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: f371d5cd4e20096d5101fadf9066e3a135218d0b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 的连接库
本文列出了开发人员在开发要连接到的应用程序和查询 Azure Database for PostgreSQL 时可使用的库和驱动程序。

## <a name="client-interfaces"></a>客户端接口
用于连接到 PostgreSQL 服务器的大多数语言客户端库为外部项目，并且独立分发。 Windows、Linux 和 Mac 平台支持所列出的库连接到 Azure Database for PostgreSQL。 “后续步骤”部分中列出了几个快速入门示例。

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
阅读这些快速入门，了解如何使用所选语言连接和查询 Azure Database for PostgreSQL：

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
