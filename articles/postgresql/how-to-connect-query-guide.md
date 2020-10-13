---
title: 连接和查询-单服务器 PostgreSQL
description: 指向 Azure SQL 数据库快速入门的链接，其中显示了如何连接到服务器并运行查询。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 924dbadc07f57e5928ecc63a24bf5e57d6213670
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934200"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>适用于 PostgreSQL 的 Azure 数据库的连接和查询概述-单服务器

以下文档包含指向示例的链接，这些示例演示如何连接和查询 Azure Database for PostgreSQL 单一服务器。 本指南还包括可用于连接到服务器的 TLS 建议和扩展，如下所支持的语言。

## <a name="quickstarts"></a>快速入门

| 快速入门 | 说明 |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|您可以使用 pgadmin 连接到服务器，并简化数据库对象的创建、维护和使用。|
|[Azure Cloud Shell 中的 psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|本文介绍如何在[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)中运行[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html)以连接到服务器，然后运行语句以在数据库中查询、插入、更新和删除数据。如果已在开发环境中安装，则可以运行**psql**|
|[PostgreSQL 与 VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|适用于 VS Code (预览版的 Azure 数据库扩展) 允许使用 scrapbooks 和丰富的 Intellisense 在本地和云中浏览和查询 PostgreSQL 服务器。 |
|[PHP](connect-php.md)|本快速入门演示如何使用 PHP 来创建连接到数据库的程序，并使用数据库对象处理数据。|
|[Java](connect-java.md)|本快速入门演示了如何使用 Java 连接到数据库，然后使用数据库对象处理数据。|
|[Node.js](connect-nodejs.md)|本快速入门演示了如何使用 Node.js 创建用于连接数据库的程序，并使用数据库对象处理数据。|
|[.NET (C # ) ](connect-csharp.md)|本快速入门演示了如何 use.NET c # )  (c # 创建用于连接到数据库的 c # 程序，并使用数据库对象处理数据。|
|[Go](connect-go.md)|本快速入门演示如何使用 Go 连接到数据库。 此外演示了用于查询和修改数据的 Transact-SQL 语句。|
|[Python](connect-python.md)|本快速入门演示如何使用 Python 连接到数据库，并使用数据库对象处理数据。 |
|[Ruby](connect-ruby.md)|本快速入门演示如何使用 Ruby 创建连接到数据库的程序，并使用数据库对象处理数据。|


## <a name="tls-considerations-for-database-connectivity"></a>数据库连接的 TLS 注意事项

传输层安全 (TLS) 由 Microsoft 为连接到 Azure Database for PostgreSQL 中的数据库提供或支持的所有驱动程序使用。 不需要特殊配置，但对于新创建的服务器，请强制执行 TLS 1.2。 如果你使用的是 TLS 1.0 和1.1，则建议你更新服务器的 TLS 版本。 请参阅 [ 如何配置 TLS](howto-tls-configurations.md)


## <a name="postgresql-extensions"></a>PostgreSQL 扩展
PostgreSQL 支持使用扩展来扩展数据的功能。 扩展在单个包中将多个相关 SQL 对象捆绑在一起，可以使用单个命令在数据库中加载或删除该包。 在数据库中加载之后，扩展会如同内置功能一样运行。

- [Postgres 11 扩展](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-11-extensions)
- [Postgres 10 扩展](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-10-extensions)
- [Postgres 9.6 扩展](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-96-extensions)
- [Postgres 9.5 扩展](https://docs.microsoft.com/azure/postgresql/concepts-extensions#postgres-95-extensions)

有关更多详细信息，请参阅 [如何在单一服务器上使用 PostgreSQL 扩展](concepts-extensions.md)。

## <a name="next-steps"></a>后续步骤 

- [使用转储和还原迁移数据](howto-migrate-using-dump-and-restore.md)
- [使用导入和导出迁移数据](howto-migrate-using-export-and-import.md)
