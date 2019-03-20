---
title: Azure Database for MariaDB 驱动程序和管理工具兼容性
description: 本文介绍的 MariaDB 驱动程序和使用 Azure Database for MariaDB 兼容的管理工具。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 36628ed0749814b5bafe4b18278c8bcfe7265374
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227548"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB 驱动程序和管理工具与 Azure Database for MariaDB 兼容

本文介绍的驱动程序和使用 Azure Database for MariaDB 兼容的管理工具。

## <a name="mariadb-drivers"></a>MariaDB 驱动程序

Azure Database for MariaDB 使用 MariaDB server 社区版。 因此，它与多种编程语言和驱动程序兼容。 MariaDB 的 API 和协议都与所使用的 MySQL 兼容。 这意味着工作与 MySQL 配合使用的连接器也应使用 MariaDB。

目标是支持三个最新版本 MariaDB 驱动程序，并与来自开源社区的作者努力不断改进的功能和可用性的 MariaDB 驱动程序继续。 下表中提供了经过测试和使用 Azure Database for MariaDB 10.2 兼容的驱动程序的列表：

**驱动程序** | **链接** | **兼容版本** | **不兼容版本** | **说明**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5、5.6、7.x | 5.3 | 对于 PHP 7.0 与 SSL MySQLi 的连接，请在连接字符串中添加 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT。 <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 设置：```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 选项为 false。
.NET | [GitHub 上的 MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [来自 Nuget 的安装包](https://www.nuget.org/packages/MySqlConnector/) | 0.27 及以上版本 | 0.26.5 及以下版本 |
MySQL 连接器/NET | [MySQL 连接器/NET](https://github.com/mysql/mysql-connector-net) | 8.0、7.0、6.10 |  | 编码错误可能会导致某些非 UTF8 Windows 系统的连接失败。
Node.js |  [GitHub 上的 MySQLjs](https://github.com/mysqljs/mysql/) <br> 来自 NPM 的安装包：<br> 从 NPM 运行 `npm install mysql` | 2.15 | 2.14.1 及以下版本
前往 | https://github.com/go-sql-driver/mysql/releases | 1.3 | 1.2 及以下版本 | 在连接字符串中使用 allowNativePasswords=true
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3、2.0、2.1、2.2 | 1.2.2 及以下版本 |
Java | https://downloads.mariadb.org/connector-java/ | 2.1、2.0、1.6 | 1.5.5 及以下版本 |

## <a name="management-tools"></a>管理工具

兼容性优势也适用于数据库管理工具。 现有工具应继续使用 Azure 数据库的 MariaDB，只要数据库操作在范围内的用户权限。 下表列出了三个常见数据库管理工具的已测试并发现要使用 Azure Database for MariaDB 10.2 兼容：

| | **MySQL Workbench 6.x 及以上版本** | **Navicat 12** | **PHPMyAdmin 4.x 及以上版本**
---|---|---|---
创建、更新、读取、写入、删除 | X | X | X
SSL 连接 | X | X | X
SQL 查询自动完成 | X | X |
导入和导出数据 | X | X | X
导出为多种格式 | X | X | X
备份和还原 |  | X |
显示服务器参数 | X | X | X
显示客户端连接 | X | X | X

## <a name="next-steps"></a>后续步骤

- [解决 Azure Databases for MariaDB 的连接问题](howto-troubleshoot-common-connection-issues.md)