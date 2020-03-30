---
title: 连接库 - MySQL 的 Azure 数据库
description: 本文列出了客户端程序连接到 Azure Database for MySQL 时可以使用的每个库或驱动程序。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537187"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Azure Database for MySQL 的连接库
本文列出了客户端程序连接到 Azure Database for MySQL 时可以使用的每个库或驱动程序。

## <a name="client-interfaces"></a>客户端接口
MySQL 提供标准数据库驱动程序连接，以将 MySQL 与符合行业标准 ODBC 和 JDBC 的应用程序和工具配合使用。 适用于 ODBC 或 JDBC 的任何系统都可以使用 MySQL。

| **语言** | **平台** | 其他资源**** | **下载** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows、Linux | [MySQL native driver for PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/)（适用于 PHP 的 MySQL 本机驱动器 - mysqlnd） | [下载](https://secure.php.net/downloads.php) |
| ODBC | Windows、Linux、Mac OS X 和 Unix 平台 | [MySQL Connector/ODBC Developer Guide](https://dev.mysql.com/doc/connector-odbc/en/)（MySQL 连接器/ODBC 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/Net Developer Guide](https://dev.mysql.com/doc/connector-net/en/)（MySQL 连接器/Net 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | 平台独立 | [MySQL Connector/J 5.1 Developer Guide](https://dev.mysql.com/doc/connector-j/5.1/en/)（MySQL 连接器/J 5.1 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows、Linux、Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [下载](https://github.com/sidorares/node-mysql2) |
| Python | Windows、Linux、Mac OS X | [MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/)（MySQL 连接器/Python 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows、Linux、Mac OS X | [MySQL Connector/C++ Developer Guide](https://dev.mysql.com/doc/connector-cpp/en/)（MySQL 连接器/C++ 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows、Linux、Mac OS X | [MySQL 连接器/C 开发人员指南](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [下载](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows、Linux、Mac OS X 和 Unix 平台 | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [下载](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>后续步骤
阅读这些快速入门，了解如何使用所选语言连接和查询 Azure Database for MySQL：

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET （C#）](./connect-csharp.md) | [Python](./connect-python.md) | [节点.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [去](./connect-go.md)

