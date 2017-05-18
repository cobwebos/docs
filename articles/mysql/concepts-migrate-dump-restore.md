---
title: "在 Azure Database for MySQL 中使用转储和还原来迁移 MySQL 数据库 | Microsoft Docs"
description: "介绍迁移 Azure Database for MySQL。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c09a6fa947d235189ab0137b074b6d7d9c925827
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>使用转储和还原将 MySQL 数据库迁移到 Azure Database for MySQL
本文介绍了在 Azure Database for MySQL 中备份和还原数据库的两种常见方式
- 从命令行（使用 mysqldump）进行备份和还原 
- 使用 PHPMyAdmin 进行备份和还原 

## <a name="before-you-begin"></a>开始之前
若要逐步执行本操作方法指南，需要具备以下条件：
- [创建 Azure Database for MySQL 服务器 - Azure 门户](quickstart-create-mysql-server-database-using-azure-portal.md)
- 已在计算机上安装 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 命令行实用程序
- MySQL Workbench [MySQL Workbench 下载](https://dev.mysql.com/downloads/workbench/)、Toad、Navicat 或任何第三方 MySQL 工具

## <a name="use-common-tools"></a>使用常用工具
使用常用工具（例如 MySQL Workbench、mysqldump、Toad 或 Navicat）进行远程连接，并将数据还原到 Azure Database for MySQL 中。 在具有 Internet 连接的客户端计算机上使用此类工具连接到 Azure Database for MySQL。 使用具有 SSL 加密的连接是最安全的做法，另请参阅[在 Azure Database for MySQL 中配置 SSL 连接](concepts-ssl-connection-security.md)。 迁移到 Azure Database for MySQL 时，无需将转储文件移到任何特殊的云位置。 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>使用 mysqldump 从命令行创建备份文件
若要在本地或某个 VM 中备份现有 MySQL 数据库，请运行以下命令： 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

需要提供的参数包括：
- [uname] 你的数据库用户名 
- [pass] 数据库密码（请注意，-p 和密码之间没有空格） 
- [dbname] 你的数据库名称 
- [backupfile.sql] 数据库备份的文件名 
- [--opt] mysqldump 选项 

例如，若要将名为“testdb”、用户名为“testuser”且无密码的数据库备份到文件 testdb_backup.sql，请使用以下命令。 此命令会将“testdb”数据库备份到名为 testdb_backup.sql 的文件中，该文件将包含重新创建该数据库所需的所有 SQL 语句。 

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

## <a name="upload-files"></a>上传文件
使用 WinSCP，可以通过 SFTP 协议或 FTPS 协议（用于导出目的）轻松上传和管理本地现有 MySQL 环境（Azure 或非 Azure）文件的导入和转储。

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>在目标 Azure MySQL 服务器上创建数据库
必须在目标 Azure Database for MySQL 服务器（要在其中使用面向 MySQL 的 MySQL Workbench、Toad、Navicat 或任何第三方工具迁移数据）上创建一个空数据库。 数据库名称可与包含转储数据的数据库名称相同，或可以创建一个不同名称的数据库。

![Azure Database for MySQL 连接字符串](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench 连接字符串](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>使用命令行或 MySQL Workbench 还原 MySQL 数据库
创建目标数据库后，可以使用 mysql 命令或 MySQL Workbench 将数据从转储文件还原到新创建的特定数据库。
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
在此示例中，我们会将数据还原到目标服务器上新创建的数据库 testdb3。
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>使用 PHPMyAdmin 进行导出
若要导出，可以使用可能已在本地环境中安装的常见工具 phpMyAdmin。 使用 PHPMyAdmin 导出 MySQL 数据库：
- 打开 phpMyAdmin。
- 通过单击屏幕左侧列表中的数据库名称，选择你的数据库。 
- 单击“导出”链接。 此操作将显示新的屏幕，其中显示“查看数据库转储”。 
- 在导出区域中，单击“全选”链接，选择数据库中的所有表。 
- 在 SQL 选项区域中，单击正确的选项。 
- 单击“另存为文件”选项以及相应的压缩选项，然后单击“Go”按钮。 将出现一个对话框，提示你在本地保存该文件。

## <a name="import-using-phpmyadmin"></a>使用 PHPMyAdmin 进行导入
导入数据库的方法与导出类似。 执行以下操作：
- 打开 phpMyAdmin。 
- 创建适当命名的数据库，并通过单击屏幕左侧列表中的数据库名选择该数据库。 如果想要重写现有数据库的导入，请单击数据库名，选择表名旁边的所有复选框，然后选择“删除”，删除数据库中现有的所有表。 
- 单击 SQL 链接。 此操作将显示一个新的屏幕，可在其中键入 SQL 命令或上传 SQL 文件。 
- 使用“浏览”按钮查找数据库文件。 
- 单击“Go”按钮。 这将导出备份、执行 SQL 命令并重新创建数据库。

## <a name="next-steps"></a>后续步骤

[使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md) 
[使用 Azure CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)

