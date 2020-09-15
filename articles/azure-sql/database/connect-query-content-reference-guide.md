---
title: 连接和查询
description: 指向 Azure SQL 数据库快速入门的链接，介绍如何连接到以及查询 Azure SQL 数据库和 Azure SQL 托管实例。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 2a6aa0aa22fce5ba0f763f98fec37f607d0ce8a1
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441759"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL 数据库和 Azure SQL 托管实例连接和查询文章
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

以下文档包含 Azure 示例的链接，演示如何连接和查询 Azure SQL 数据库以及 Azure SQL 托管实例。 有关传输级别安全性的相关建议，请参阅[数据库连接的 TLS 注意事项](#tls-considerations-for-database-connectivity)。

## <a name="quickstarts"></a>快速入门

| 快速入门 | 说明 |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|本快速入门演示如何使用 SSMS 连接到数据库，并使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|本快速入门演示如何使用 Azure Data Studio 连接到数据库，然后使用 Transact-SQL (T-SQL) 语句创建在 Azure Data Studio 教程中使用的 TutorialDB。|
|[Azure 门户](connect-query-portal.md)|本快速入门演示如何使用查询编辑器连接到数据库（仅限 Azure SQL 数据库），然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[Visual Studio Code](connect-query-vscode.md)|本快速入门演示如何使用 Visual Studio Code 连接到数据库，并使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[将 .NET 与 Visual Studio 配合使用](connect-query-dotnet-visual-studio.md)|本快速入门演示如何使用 .NET framework 与 Visual Studio 来创建连接到数据库的 C# 程序，并使用 Transact-SQL 语句查询数据。|
|[.NET Core](connect-query-dotnet-core.md)|本快速入门演示如何在 Windows/Linux/macOS 中使用 .NET Core 创建连接到数据库的 C# 程序，并使用 Transact-SQL 语句查询数据。|
|[Go](connect-query-go.md)|本快速入门演示如何使用 Go 连接到数据库。 此外演示了用于查询和修改数据的 Transact-SQL 语句。|
|[Java](connect-query-java.md)|本快速入门演示如何使用 Java 连接到数据库，并使用 Transact-SQL 语句查询数据。|
|[Node.js](connect-query-nodejs.md)|本快速入门演示如何使用 Node.js 创建连接到数据库的程序，并使用 Transact-SQL 语句查询数据。|
|[PHP](connect-query-php.md)|本快速入门演示如何使用 PHP 创建连接到数据库的程序，并使用 Transact-SQL 语句来查询数据。|
|[Python](connect-query-python.md)|本快速入门演示如何使用 Python 连接到 Azure SQL 数据库，并使用 Transact-SQL 语句查询数据。 |
|[Ruby](connect-query-ruby.md)|本快速入门演示如何使用 Ruby 创建连接到数据库的程序，并使用 Transact-SQL 语句查询数据。|
|[R](connect-query-r.md)|本快速入门演示如何将 R 与 Azure SQL 数据库机器学习服务配合使用来创建连接到 Azure SQL 数据库中数据库的程序并使用 Transact-SQL 语句查询数据。|
|||

## <a name="tls-considerations-for-database-connectivity"></a>数据库连接的 TLS 注意事项

Microsoft 提供或支持的所有驱动程序使用传输层安全性 (TLS) 连接到 Azure SQL 数据库中的数据库或 Azure SQL 托管实例。 无需特殊配置。 对于到 SQL Server 实例、Azure SQL 数据库中的数据库，或 Azure SQL 托管实例的实例的所有连接，我们建议所有应用程序设置以下配置或其等效项：

- **Encrypt = On**
- **TrustServerCertificate = Off**

某些系统对于这些配置关键字使用不同但等效的关键字。 这些配置可确保客户端驱动程序验证从服务器收到的 TLS 证书的标识。

如果需要符合支付卡行业 - 数据安全标准 (PCI-DSS)，我们还建议你在客户端上禁用 TLS 1.1 和 1.0。

默认情况下，非 Microsoft 驱动程序可能不会使用 TLS。 连接到 Azure SQL 数据库或 Azure SQL 托管实例时，这可能是一个因素。 使用嵌入式驱动程序的应用程序可能不允许你控制这些连接设置。 我们建议你在与敏感数据进行交互的系统上使用此类驱动程序和应用程序之前，检查这些程序的安全性。

## <a name="libraries"></a>库

可以使用各种库和框架连接到 Azure SQL 数据库或 Azure SQL 托管实例。 请查看[快速入门教程](https://aka.ms/sqldev)，了解 C#、Java、Node.js、PHP、Python 等编程语言的快速入门方式。 然后使用基于 Linux 或 Windows 的 SQL Server 或基于 macOS 的 Docker 生成一个应用。

下表列出的连接库或驱动程序可供客户端应用程序用于通过多种语言连接到在本地或云中运行的 SQL Server 并使用该 SQL Server。 可以在 Linux、Windows 或 Docker 上使用它们，连接到 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics（以前称为 SQL 数据仓库）。

| 语言 | 平台 | 其他资源 | 下载 | 入门 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [下载](https://www.microsoft.com/net/download/) | [入门](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft JDBC 驱动程序](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [下载](https://go.microsoft.com/fwlink/?linkid=852460) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [用于 SQL Server 的 PHP SQL 驱动程序](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [下载](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [入门](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [用于 SQL Server 的 Node.js 驱动程序](/sql/connect/node-js/node-js-driver-for-sql-server/) | [安装](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驱动程序](/sql/connect/python/python-driver-for-sql-server/) | 安装选项： <br/> \* [pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \* [pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [用于 SQL Server 的 Ruby 驱动程序](/sql/connect/ruby/ruby-driver-for-sql-server/) | [安装](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [入门](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft ODBC 驱动程序](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [下载](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

下表列出了客户端应用程序可以与 SQL Server、Azure SQL 数据库、Azure SQL 托管实例或 Azure Synapse Analytics 一起使用的对象关系映射 (ORM) 框架和 Web 框架的示例。 可以在 Linux、Windows 或 Docker 上使用这些框架。

| 语言 | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [实体框架](https://docs.microsoft.com/ef)<br>[实体框架核心](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows、Linux、macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>后续步骤

- 有关连接体系结构信息，请参阅 [Azure SQL 数据库连接体系结构](connectivity-architecture.md)。
- 查找用于从客户端应用程序进行连接的 [SQL Server 驱动程序](/sql/connect/sql-connection-libraries/)。
- 连接到 Azure SQL 数据库或 Azure SQL 托管实例：
  - [使用 .NET (C#) 进行连接和查询](connect-query-dotnet-core.md)
  - [使用 PHP 进行连接和查询](connect-query-php.md)
  - [使用 Node.js 进行连接和查询](connect-query-nodejs.md)
  - [使用 Java 进行连接和查询](connect-query-java.md)
  - [使用 Python 进行连接和查询](connect-query-python.md)
  - [使用 Ruby 进行连接和查询](connect-query-ruby.md)
- 重试逻辑代码示例：
  - [使用 ADO.NET 进行弹性连接][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [使用 PHP 进行弹性连接][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
