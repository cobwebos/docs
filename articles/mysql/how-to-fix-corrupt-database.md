---
title: 解决数据库损坏-Azure Database for MySQL
description: 在本文中，你将了解如何修复 Azure Database for MySQL 中的数据库损坏问题。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766895"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的数据库损坏的疑难解答
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

数据库损坏可能导致应用程序停机。 同时解决损坏问题并避免数据丢失也至关重要。 当数据库损坏时，您的服务器日志中会出现此错误： `InnoDB: Database page corruption on disk or a failed.`

本文介绍如何解决数据库或表损坏问题。 Azure Database for MySQL 使用 InnoDB 引擎。 它具有自动损坏检查和修复操作的功能。 InnoDB 通过在其读取的每一页上运行校验和来检查损坏的页。 如果发现校验和差异，它会自动停止 MySQL 服务器。

请尝试以下选项来快速缓解数据库损坏问题。

## <a name="restart-your-mysql-server"></a>重新启动 MySQL 服务器

您通常会注意到，当您的应用程序访问表或数据库时，数据库或表损坏。 InnoDB 是一种崩溃恢复机制，可在重新启动服务器时解决大多数问题。 因此重新启动服务器可以帮助服务器从导致数据库处于错误状态的故障中恢复。

## <a name="use-the-dump-and-restore-method"></a>使用转储和还原方法

建议使用 *转储和还原* 方法解决损坏问题。 此方法涉及：
1. 访问损坏的表。
1. 使用 mysqldump 实用工具创建表的逻辑备份。 备份将保留表的结构和数据。
1. 将表重新加载到数据库。

### <a name="back-up-your-database-or-tables"></a>备份数据库

> [!Important]
> - 请确保已将防火墙规则配置为从客户端计算机访问服务器。 有关详细信息，请参阅在 [单个服务器上配置防火墙规则](howto-manage-firewall-using-portal.md) 和 [在灵活的服务器上配置防火墙规则](flexible-server/how-to-connect-tls-ssl.md)。
> - `--ssl-cert`如果已启用 ssl，请使用 mysqldump 的 ssl 选项。

使用 mysqldump 从命令行创建备份文件。 使用此命令：

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

参数说明：
- `[ssl-cert=/path/to/pem]`： SSL 证书的路径。 在客户端计算机上下载 SSL 证书，并在命令中设置其路径。 如果禁用 SSL，请不要使用此参数。
- `[host]`：你的 Azure Database for MySQL 服务器。
- `[uname]`：你的服务器管理员用户名。
- `[pass]`：管理员用户的密码。
- `[dbname]`：数据库的名称。
- `[backupfile.sql]`：数据库备份的文件名。

> [!Important]
> - 对于 "单一服务器"，请使用 `admin-user@servername` `myserveradmin` 以下命令中要替换的格式：
> - 对于 "灵活服务器"，请使用 `admin-user` `myserveradmin` 以下命令中要替换的格式：

如果特定表损坏，请选择要备份的数据库中的特定表：
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

若要备份一个或多个数据库，请使用 `--database` 开关，并列出数据库名称，用空格分隔：

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>还原数据库

以下步骤说明了如何还原数据库。 创建备份文件后，可以使用 mysql 实用程序还原表或数据库。 运行以下命令：

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
以下示例 `testdb` 从使用 mysqldump 创建的备份文件进行还原： 

> [!Important]
> - 对于 "单一服务器"，请使用 `admin-user@servername` `myserveradmin` 以下命令中要替换的格式：
> - 对于灵活的服务器，请使用 ```admin-user``` 以下命令中要替换的格式： `myserveradmin` 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>后续步骤
如果前面的步骤未解决此问题，你始终可以还原整个服务器：
- [Azure Database for MySQL-单服务器中的还原服务器](howto-restore-server-portal.md)
- [Azure Database for MySQL-灵活服务器中的还原服务器](flexible-server/how-to-restore-server-portal.md)



