---
title: "如何在 Azure Database for PostgreSQL 中转储和还原 | Microsoft Docs"
description: "介绍如何在 Azure Database for PostgreSQL 中将 PostgreSQL 数据库解压缩为转储文件，以及如何从 pg_dump 创建的存档文件还原 PostgreSQL 数据库。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b056b2aee8b3d82077f61c18e3a660f3562ae068
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>使用转储和还原迁移 PostgreSQL 数据库
可以使用 [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) 将 PostgreSQL 数据库提取到转储文件，并使用 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) 从 pg_dump 创建的存档文件中还原 PostgreSQL 数据库。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 一个 [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)，其防火墙规则设置为允许访问，并且包含数据库。
- 已安装 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 和 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 命令行实用程序

请按照以下步骤转储并还原 PostgreSQL 数据库。

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
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
在本示例中，我们将数据从转储文件生成的 testdb.dump 中还原到目标服务器 mypgserver-20170401.postgres.database.azure.com 上的数据库 mypgsqldb
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>后续步骤
- 若要使用导出和导入迁移 PostgreSQL 数据库，请参阅[使用导入和导出迁移 PostgreSQL 数据库](howto-migrate-using-export-and-import.md)
