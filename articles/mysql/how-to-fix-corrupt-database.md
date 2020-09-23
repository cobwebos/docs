---
title: 解决数据库损坏-Azure Database for MySQL
description: 了解如何解决 Azure Database for MySQL 的数据库损坏问题
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934249"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>排查 Azure Database for MySQL 上的数据库损坏
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

数据库损坏可能导致应用程序停机，同时解决问题并避免数据丢失也至关重要。 出现数据库损坏时，您的服务器中会显示此错误 **InnoDB：磁盘上的数据库页损坏或失败**。

在本指南中，您将了解如何修复数据库或表是否已损坏。 Azure Database for MySQL 使用 InnoDB 引擎，它具有自动损坏检查和修复操作的功能。 InnoDB 通过对其读取的每个页面执行校验和来检查损坏的页面，如果发现校验和差异，它会自动停止 MySQL 服务器。

请尝试下列任一选项，以帮助快速缓解数据库损坏问题。

## <a name="restart-your-mysql-server"></a>重新启动 MySQL 服务器

您通常会注意到，当您的应用程序访问该表的 "只读数据库" 时，数据库或表已损坏。 由于 InnoDB 具有一种崩溃恢复机制，可以在重新启动服务器时解决大多数问题。 因此，重新启动服务器应该有助于服务器从导致数据库处于错误状态的故障中恢复。

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>用转储和还原方法解决数据损坏

建议使用 **转储和还原方法**解决损坏问题。 这涉及到对损坏的表的访问权限，使用 **mysqldump** 实用工具创建该表的逻辑备份，这将保留表结构和其中的数据，然后将该表重新加载到数据库中。

### <a name="backup-your-database-or-tables"></a>备份数据库

> [!Important]
> - 使你配置了防火墙规则，以便从客户端计算机访问服务器。 请参阅如何[在灵活的服务器](flexible-server/how-to-connect-tls-ssl.md)上配置[单台服务器上的防火墙规则](howto-manage-firewall-using-portal.md)和防火墙规则。
> - ```--ssl-cert```如果启用 ssl，则使用适用于**MYSQLDUMP**的 ssl 选项

使用 mysqldump 通过命令行使用此命令创建备份文件

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

需要提供的参数包括：
- [ssl-cert =/path/to/pem]在客户端计算机上下载 SSL 证书，并在命令中设置其路径。 不要使用这种情况，SSL 处于禁用状态。
- [host] 是你的 Azure Database for MySQL 服务器
- [uname] 是你的服务器管理员用户名
- [pass] 是管理员用户的密码
- [dbname] 是你的数据库的名称
- [backupfile] 如果你的数据库备份的文件名

> [!Important]
> - 对于单一服务器，请使用 ```admin-user@servername``` ```myserveradmin``` 以下命令中的格式替换。
> - 对于灵活的服务器，请使用 ```admin-user``` ```myserveradmin``` 以下命令中的格式替换。

如果特定表已损坏，请使用此示例选择要备份的数据库中的特定表
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

若要备份一个或多个数据库，请使用--database 开关并列出用空格分隔的数据库名称。

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>还原数据库

以下步骤说明如何还原数据库或表。 创建备份文件后，可以使用 ***mysql** 实用程序还原表或数据库。 运行命令，如下所示：

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
下面是 ```testdb``` 使用 **mysqldump**创建的从备份文件还原的示例。 

> [!Important]
> - 对于 "单一服务器"，请使用 ```admin-user@servername``` ```myserveradmin``` 以下命令中的替换格式。
> - 对于灵活的服务器，请使用 ```admin-user``` ```myserveradmin``` 以下命令中的替换格式：。 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>后续步骤
如果上述步骤不能帮助解决此问题，你始终可以还原整个服务器。
- [还原 Azure Database for MySQL 单个服务器](howto-restore-server-portal.md)
- [还原 Azure Database for MySQL 灵活的服务器](flexible-server/how-to-restore-server-portal.md)



