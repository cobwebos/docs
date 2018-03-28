---
title: 如何在 Azure Database for PostgreSQL 中转储和还原
description: 介绍如何在 Azure Database for PostgreSQL 中将 PostgreSQL 数据库解压缩为转储文件，以及如何从 pg_dump 创建的文件进行还原。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f74c60cb99ee5bae1af8e000ebbd21b41600638d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>使用转储和还原迁移 PostgreSQL 数据库
可以使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 将 PostgreSQL 数据库提取到转储文件，并使用 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) 从 pg_dump 创建的存档文件中还原 PostgreSQL 数据库。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 一个 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)，其防火墙规则设置为允许访问，并且包含数据库。
- 已安装 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 命令行实用程序

按照以下步骤转储并还原 PostgreSQL 数据库：

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>使用 pg_dump 创建转储文件，该文件包含要加载数据
若要在本地或某个 VM 中备份现有 PostgreSQL 数据库，请运行以下命令：
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
例如，如果有一个本地服务器，并且该服务器中包含一个名为 testdb 的数据库
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>使用 pg_restore 将数据还原到目标 Azure Database for PostrgeSQL
创建目标数据库后，可以使用 pg_restore 命令和 -d、--dbname 参数将数据从转储文件还原到目标数据库。
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
包括 --no-owner 参数会导致还原过程中创建的所有对象由使用 --username 指定的用户拥有。 有关详细信息，请参阅有关 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 的正式 PostgreSQL 文档。

在此示例中，将数据从转储文件 **testdb.dump** 还原到目标服务器 **mydemoserver.postgres.database.azure.com** 上的数据库 **mypgsqldb**。 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>后续步骤
- 若要使用导出和导入迁移 PostgreSQL 数据库，请参阅[使用导入和导出迁移 PostgreSQL 数据库](howto-migrate-using-export-and-import.md)