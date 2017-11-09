---
title: "在 Azure Database for MySQL 中使用转储和还原来迁移 MySQL 数据库 | Microsoft Docs"
description: "本文介绍使用 mysqldump、MySQL Workbench 和 PHPMyAdmin 等工具在 Azure Database for MySQL 中备份和还原数据库的两种常见方式。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: ce6edbdffe9704383676e990865cd4e2958f30fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>使用转储和还原将 MySQL 数据库迁移到 Azure Database for MySQL
本文介绍了在 Azure Database for MySQL 中备份和还原数据库的两种常见方式
- 从命令行转储和还原（使用 mysqldump） 
- 使用 PHPMyAdmin 转储和还原 

## <a name="before-you-begin"></a>开始之前
若要逐步执行本操作方法指南，需要具备以下条件：
- [创建 Azure Database for MySQL 服务器 - Azure 门户](quickstart-create-mysql-server-database-using-azure-portal.md)
- 已在计算机上安装 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 命令行实用程序。
- 用于执行转储和还原命令的 MySQL Workbench [MySQL Workbench 下载](https://dev.mysql.com/downloads/workbench/)、Toad、Navicat 或其他第三方 MySQL 工具。

## <a name="use-common-tools"></a>使用常用工具
使用常见的实用程序和工具（例如 MySQL Workbench、mysqldump、Toad 或 Navicat）进行远程连接，并将数据还原到 Azure Database for MySQL 中。 在具有 Internet 连接的客户端计算机上使用此类工具连接到 Azure Database for MySQL。 使用具有 SSL 加密的连接是最安全的做法，另请参阅[在 Azure Database for MySQL 中配置 SSL 连接](concepts-ssl-connection-security.md)。 迁移到 Azure Database for MySQL 时，无需将转储文件移到任何特殊的云位置。 

## <a name="common-uses-for-dump-and-restore"></a>转储和还原的常见用途
在几个常见方案中，可使用 mysqldump、mysqlpump 等 MySQL 实用程序以将数据库转储和加载到 Azure MySQL 数据库。 在其他方案中，可改用[导入和导出](concepts-migrate-import-export.md)方法。

- 迁移整个数据库时请使用数据库转储。 此建议适用于移动大量 MySQL 数据，或者要最小化实时站点或应用程序的服务中断的情况。 
-  将数据加载到 Azure Database for MySQL 时，请确保数据库中的所有表都使用 InnoDB 存储引擎。 Azure Database for MySQL 仅支持 InnoDB 存储引擎，因此不支持备选存储引擎。 如果表配置了其他存储引擎，请确保先将它们转换为 InnoDB 引擎格式，再迁移到 Azure Database for MySQL。
   例如，如果有使用 MyISAM 表的 WordPress 或 WebApp，在将这些表还原到 Azure Database for MySQL 之前，首先通过将这些表迁移到 InnoDB 格式的方式转换格式。 使用子句 `ENGINE=InnoDB` 设置创建新表时所用的引擎，然后在还原之前将数据传输到兼容表中。 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 若要避免任何兼容性问题，请确保转储数据库时，源和目标系统上所使用的 MySQL 版本相同。 例如，如果现有 MySQL 服务器版本是 5.7，那么应迁移到配置为运行版本 5.7 的 Azure Database for MySQL 中。 在 Azure Database for MySQL 中，`mysql_upgrade` 命令不起作用，也不受支持。 如果需要跨 MySQL 版本进行升级，应先将低版本数据库转储或导出到自己环境中更高版本的 MySQL 中。 然后运行 `mysql_upgrade`再尝试迁移到 Azure Database for MySQL 中。

## <a name="performance-considerations"></a>性能注意事项
若要优化性能，请在转储大型数据库时留意这些注意事项：
-   转储数据库时，请使用 mysqldump 中的 `exclude-triggers` 选项。 从转储文件中排除触发器，避免在还原数据期间触发触发器命令。 
-   使用 `single-transaction` 选项，将事务隔离模式设置为 REPEATABLE READ 并在转储数据之前将 START TRANSACTION SQL 语句发送到服务器。 在单个事务中转储多个表会在还原过程中占用一些额外的存储空间。 `single-transaction` 选项和 `lock-tables` 选项彼此互斥，因为 LOCK TABLES 会导致所有挂起的事务隐式提交。若要转储大型表，请将 `single-transaction` 选项和 `quick` 选项配合使用。 
-   使用其中包含多个 VALUE 列表的 `extended-insert` 多行语法。 这可使转储文件较小并在重新加载文件时加快插入。
-  转储数据库时，使用 mysqldump 中的 `order-by-primary` 选项，以便按主键顺序编写数据脚本。
-   转储数据时，使用 mysqldump 中的 `disable-keys` 选项，以便在加载前，禁用外键约束。 禁用外键检查可提高性能。 启用约束并在加载后验证数据，确保引用完整性。
-   适当时使用已分区表。
-   并行加载数据。 避免太多将导致达到资源限制的并行度，并通过使用 Azure 门户中提供的指标监视资源。 
-   转储数据库时，使用 mysqlpump 中的 `defer-table-indexes` 选项，以便在加载表数据后创建索引。

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>使用 mysqldump 从命令行创建备份文件
若要备份本地服务器或虚拟机中的现有 MySQL 数据库，请运行以下命令： 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

需要提供的参数包括：
- [uname] 数据库用户名 
- [pass] 数据库密码（请注意，-p 和密码之间没有空格） 
- [dbname] 数据库名称 
- [backupfile.sql] 数据库备份的文件名 
- [--opt] mysqldump 选项 

例如，若要将 MySQL 服务器上名为“testdb”的数据库（用户名为“testuser”且无密码）备份到文件 testdb_backup.sql，请使用以下命令。 该命令将 `testdb` 数据库备份到名为 `testdb_backup.sql` 的文件中，该文件包含重新创建数据库所需的所有 SQL 语句。 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
若要在数据库中选择特定的表进行备份，列出表名，用空格隔开。 例如，若仅从“testdb”备份 table1 和 table2 两个表，请遵循此示例： 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

若要一次性备份多个数据库，请使用 --database 切换并列出数据库名（用空格隔开）。 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
若要一次性备份服务器中的所有数据库，应使用 --all-databases 选项。
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>在 Azure Database for MySQL 目标服务器上创建数据库
在要迁移数据的 Azure Database for MySQL 目标服务器上创建一个空数据库。 使用 MySQL Workbench、Toad 或 Navicat 等工具创建数据库。 数据库名称可与包含转储数据的数据库名称相同，或可以创建一个不同名称的数据库。

若要获取连接，请在 Azure Database for MySQL 的“属性”页中找到连接信息。
![在 Azure 门户中找到连接信息](./media/concepts-migrate-dump-restore/1_server-properties-name-login.png)

将连接信息添加到 MySQL Workbench。
![MySQL Workbench 连接字符串](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>使用命令行或 MySQL Workbench 还原 MySQL 数据库
创建目标数据库后，可以使用 mysql 命令或 MySQL Workbench 将数据从转储文件还原到新创建的特定数据库。
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
在此示例中，将数据还原到在 Azure Database for MySQL 目标服务器上新创建的数据库中。
```bash
$ mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>使用 PHPMyAdmin 进行导出
若要导出，可以使用可能已安装在本地环境中的常用工具 phpMyAdmin。 使用 PHPMyAdmin 导出 MySQL 数据库：
- 打开 phpMyAdmin。
- 选择数据库。 单击左侧列表中的数据库名称。 
- 单击“导出”链接。 这将显示一个新页面，可查看数据库转储情况。
- 在“导出”区域中，单击“全选”链接，选择数据库中的表。 
- 在 SQL 选项区域中，单击适当的选项。 
- 单击“另存为文件”选项及相应的压缩选项，然后单击“执行”按钮。 将出现一个对话框，提示在本地保存该文件。

## <a name="import-using-phpmyadmin"></a>使用 PHPMyAdmin 进行导入
导入数据库的方法与导出类似。 执行以下操作：
- 打开 phpMyAdmin。 
- 在 phpMyAdmin 设置页中，单击“添加”可添加 Azure Database for MySQL 服务器。 提供连接详细信息和登录信息。
- 创建适当命名的数据库，并在屏幕左侧选中该数据库。 若要重写现有数据库，单击数据库名称，选中所有表名称旁边的复选框，然后选择“删除”，删除现有表。 
- 单击“SQL”链接，显示可在其中键入 SQL 命令或上传 SQL 文件的页面。 
- 使用“浏览”按钮查找数据库文件。 
- 单击“执行”按钮，导出备份、执行 SQL 命令并重新创建数据库。

## <a name="next-steps"></a>后续步骤
[将应用程序连接到 Azure Database for MySQL](./howto-connection-string.md)
