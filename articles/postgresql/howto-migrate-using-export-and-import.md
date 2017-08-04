---
title: "使用 Azure Database for PostgreSQL 中的导入和导出功能迁移数据库 | Microsoft Docs"
description: "介绍了如何将 PostgreSQL 数据库解压到脚本文件，以及如何将数据从该文件导入目标数据库。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/14/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e05e61ea05afc2866a618c663cb437540ab54c9b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017

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
例如，如果有一个本地服务器，并且该服务器中包含一个名为 testdb 的数据库
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postrgesql"></a>导入目标 Azure Database for PostrgeSQL 上的数据
可以使用 psql 命令行和 -d、--dbname 参数将数据导入创建的 Azure Database for PostrgeSQL，并加载 sql 文件中的数据。
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
此示例使用 psql 实用程序和先前步骤中名为 **testdb.sql** 的脚本文件，将数据导入到目标服务器 **mypgserver-20170401.postgres.database.azure.com** 上的数据库 **mypgsqldb** 中。
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>后续步骤
- 若要通过转储和还原迁移 PostgreSQL 数据库，请参阅[Migrate your PostgreSQL database using dump and restore](howto-migrate-using-dump-and-restore.md)（通过转储和还原迁移 PostgreSQL 数据库）
