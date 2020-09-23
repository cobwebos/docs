---
title: 连接和查询-单服务器 MySQL
description: 指向 Azure SQL 数据库快速入门的链接，其中显示了如何连接到服务器并运行查询。
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 229011f11ad6898555f59b063910d80a679070e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934766"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>适用于 Azure database for MySQL 的连接和查询概述-单台服务器
以下文档包含指向示例的链接，这些示例演示如何连接和查询 Azure Database for MySQL 单一服务器。 本指南还包括可用于在下面支持的语言中连接到服务器的 TLS 建议和库。

## <a name="quickstarts"></a>快速入门

| 快速入门 | 说明 |
|---|---|
|[MySQL 工作台](connect-workbench.md)|本快速入门演示如何使用 MySQL 工作台客户端连接到数据库。 然后，可以使用 MySQL 语句在数据库中查询、插入、更新和删除数据。|
|[Azure Cloud Shell](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|本文介绍如何在[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)中运行**mysql.exe**以连接到服务器，然后运行语句以在数据库中查询、插入、更新和删除数据。|
|[带有 Visual Studio 的 MySQL](https://www.mysql.com/why-mysql/windows/visualstudio)|可以使用 MySQL for Visual Studio 连接到 MySQL 服务器。 用于 Visual Studio 的 MySQL 直接集成到服务器资源管理器使设置新连接和使用数据库对象变得轻松。|
|[PHP](connect-php.md)|本快速入门演示如何使用 PHP 来创建连接到数据库的程序，并使用 MySQL 语句来查询数据。|
|[Java](connect-java.md)|本快速入门演示了如何使用 Java 连接到数据库，然后使用 MySQL 语句来查询数据。|
|[Node.js](connect-nodejs.md)|本快速入门演示了如何使用 Node.js 创建用于连接数据库的程序，并使用 MySQL 语句来查询数据。|
|[.NET (C # ) ](connect-csharp.md)|本快速入门演示了如何 use.NET c # )  (c # 创建用于连接到数据库的 c # 程序，并使用 MySQL 语句来查询数据。|
|[Go](connect-go.md)|本快速入门演示如何使用 Go 连接到数据库。 此外演示了用于查询和修改数据的 Transact-SQL 语句。|
|[Python](connect-python.md)|本快速入门演示如何使用 Python 连接到数据库，并使用 MySQL 语句来查询数据。 |
|[Ruby](connect-ruby.md)|本快速入门演示如何使用 Ruby 创建连接到数据库的程序，并使用 MySQL 语句来查询数据。|
|[C++](connect-cpp.md)|本快速入门演示如何使用 c + + 来创建用于连接到数据库的程序并使用查询数据。|


## <a name="tls-considerations-for-database-connectivity"></a>数据库连接的 TLS 注意事项

传输层安全 (TLS) 由 Microsoft 为连接到 Azure Database for MySQL 中的数据库提供或支持的所有驱动程序使用。 不需要特殊配置，但对于新创建的服务器，请强制执行 TLS 1.2。 如果你使用的是 TLS 1.0 和1.1，则建议你更新服务器的 TLS 版本。 请参阅 [ 如何配置 TLS](howto-tls-configurations.md)


## <a name="libraries"></a>库

Azure Database for MySQL 使用世界上最常用的 MySQL 数据库社区版。 因此，它与各种编程语言和驱动程序兼容。 目标是支持三个最新版本的 MySQL 驱动程序，并且与来自开源社区的创建者共同努力，不断改进 MySQL 驱动程序的功能和可用性。

查看哪些 [驱动程序](concepts-compatibility.md) 与 Azure Database for MySQL 单一服务器兼容。 


## <a name="next-steps"></a>后续步骤 
- [使用转储和还原迁移数据](concepts-migrate-dump-restore.md)
- [使用导入和导出迁移数据](concepts-migrate-import-export.md)
