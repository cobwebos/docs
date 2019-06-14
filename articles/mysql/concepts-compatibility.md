---
title: Azure Database for MySQL 驱动程序和管理工具兼容性
description: 本文介绍与 Azure Database for MySQL 兼容的 MySQL 驱动程序和管理工具。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 05f48145973777052590f8d10e1a2ce1fd22ec7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525395"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>与 Azure Database for MySQL 兼容的 MySQL 驱动程序和管理工具
本文介绍与 Azure Database for MySQL 兼容的驱动程序和管理工具。

## <a name="mysql-drivers"></a>MySQL 驱动程序
Azure Database for MySQL 使用世界上最常用的 MySQL 数据库社区版。 因此，它与多种编程语言和驱动程序兼容。 目标是支持三个最新版本的 MySQL 驱动程序，并且与来自开源社区的创建者共同努力，不断改进 MySQL 驱动程序的功能和可用性。 下表提供了已测试并确认与 Azure Database for MySQL 5.6 和 5.7 兼容的驱动程序列表：

| **驱动程序** | **链接** | **兼容版本** | **不兼容版本** | **说明** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5、5.6、7.x | 5.3 | 对于 PHP 7.0 与 SSL MySQLi 的连接，请在连接字符串中添加 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT。 <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 设置：```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 选项为 false。|
| .NET | [GitHub 上的 MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [来自 Nuget 的安装包](https://www.nuget.org/packages/MySqlConnector/) | 0.27 及以上版本 | 0.26.5 及以下版本 | |
| MySQL 连接器/NET | [MySQL 连接器/NET](https://github.com/mysql/mysql-connector-net) | 8.0、7.0、6.10 |  | 编码错误可能会导致某些非 UTF8 Windows 系统的连接失败。 |
| Nodejs |  [GitHub 上的 MySQLjs](https://github.com/mysqljs/mysql/) <br> 来自 NPM 的安装包：<br> 从 NPM 运行 `npm install mysql` | 2.15 | 2.14.1 及以下版本 | |
| 前往 | https://github.com/go-sql-driver/mysql/releases | 1.3、1.4 | 1.2 及以下版本 | 在版本 1.3 的连接字符串中使用 `allowNativePasswords=true`。 版本 1.4 包含修补程序，不再需要 `allowNativePasswords=true`。 |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3、2.0、2.1、2.2 | 1.2.2 及以下版本 | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1、2.0、1.6 | 1.5.5 及以下版本 | |

## <a name="management-tools"></a>管理工具
兼容性优势也适用于数据库管理工具。 只要数据库操作在用户权限范围内，现有工具应继续与 Azure Database for MySQL 配合使用。 下表列出了已测试并确认与 Azure Database for MySQL 5.6 和 5.7 兼容的三种常用数据库管理工具：

|                                     | **MySQL Workbench 6.x 及以上版本** | **Navicat 12** | **PHPMyAdmin 4.x 及以上版本** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| 创建、更新、读取、写入、删除 | X | X | X |
| SSL 连接 | X | X | X |
| SQL 查询自动完成 | X | X |  |
| 导入和导出数据 | X | X | X |
| 导出为多种格式 | X | X | X |
| 备份和还原 |  | X |  |
| 显示服务器参数 | X | X | X |
| 显示客户端连接 | X | X | X |

## <a name="next-steps"></a>后续步骤

- [解决 Azure Databases for MySQL 的连接问题](howto-troubleshoot-common-connection-issues.md)