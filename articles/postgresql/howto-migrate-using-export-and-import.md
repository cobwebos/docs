---
title: 使用 Azure Database for PostgreSQL 中的导入和导出功能迁移数据库
description: 介绍了如何将 PostgreSQL 数据库解压到脚本文件，以及如何将数据从该文件导入目标数据库。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 5250b10c2d9645e25c5b6f6f0f34daa50608e51b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736637"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>使用导入和导出功能迁移 PostgreSQL 数据库
可以使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 将 PostgreSQL 数据库解压到脚本文件，并使用 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 将数据从该文件导入目标数据库。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 一个 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)，其防火墙规则设置为允许访问，并且包含数据库。
- 已安装 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 命令行实用工具
- 已安装 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 命令行实用工具

请按照下列步骤导出和导入 PostgreSQL 数据库。

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 创建包含要加载的数据的脚本文件
若要将本地或 VM 中现有的 PostgreSQL 数据库导出到 sql 脚本文件中，请在现有环境中运行以下命令：
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
例如，如果有一个本地服务器，并且该服务器中包含一个名为 testdb 的数据库：
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>导入目标 Azure Database for PostgreSQL 上的数据
可以使用 psql 命令行和 --dbname 参数 (-d) 将数据导入 Azure Database for PostgreSQL 服务器，并加载 sql 文件中的数据。
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
此示例使用 psql 实用程序和前一步骤中名为 **testdb.sql** 的脚本文件，将数据导入到目标服务器 **mydemoserver.postgres.database.azure.com** 上的数据库 **mypgsqldb** 中。
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>后续步骤
- 若要通过转储和还原迁移 PostgreSQL 数据库，请参阅[通过转储和还原迁移 PostgreSQL 数据库](howto-migrate-using-dump-and-restore.md)。
- 有关将数据库迁移到 Azure Database for PostgreSQL 的详细信息，请参阅[数据库迁移指南](http://aka.ms/datamigration)。 
