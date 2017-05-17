---
title: "Azure Database for PostgreSQL 的连接库 | Microsoft Docs"
description: "列出了客户端程序连接到 Azure Database for PostgreSQL 时可以使用的每个库或驱动程序。"
keywords: "azure 云 postgresql postgres"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 的连接库
本主题列出了客户端程序连接到 Azure Database for PostgreSQL 时可以使用的每个库或驱动程序。

## <a name="client-interfaces"></a>客户端接口
要连接到 PostgreSQL 服务器的大多数语言客户端库为外部项目，并且独立分发。 Windows、Linux 和 Mac 平台支持这些客户端库。 下面列出了一些常用的客户端驱动程序。
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
- 有关该服务的概述，请参阅 [Azure Database for PostgreSQL 概述](overview.md)。
- 有关服务器的详细信息，请参阅 [Azure Database for PostgreSQL 服务器](concepts-servers.md)。
- 若要创建第一个 PostgreSQL 服务器，请参阅[在 Azure 门户中创建 Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)。

