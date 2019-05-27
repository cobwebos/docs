---
title: Azure SQL 数据库连接和查询快速入门 | Microsoft Docs
description: 演示如何连接和查询 Azure SQL 数据库的 Azure SQL 数据库快速入门。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a8513344c35c14ebf06f3693da618ed20047d07b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792051"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>快速入门：Azure SQL 数据库连接和查询

以下文档包含演示如何连接和查询 Azure SQL 数据库的 Azure 示例链接。 它还针对传输级别安全性提供了一些建议。

## <a name="quickstarts"></a>快速入门

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|本快速入门演示了如何使用 SSMS 连接到 Azure SQL 数据库，并使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|此快速入门演示如何使用 Azure Data Studio 连接到 Azure SQL 数据库，然后使用 Transact-SQL (T-SQL) 语句创建在 Azure Data Studio 教程中使用的 TutorialDB。|
|[Azure 门户](sql-database-connect-query-portal.md)|本快速入门演示了如何使用查询编辑器连接到 SQL 数据库，然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|本快速入门演示了如何使用 Visual Studio Code 连接到 Azure SQL 数据库，并使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[将 .NET 与 Visual Studio 配合使用](sql-database-connect-query-dotnet-visual-studio.md)|此快速入门演示如何使用 .NET framework 与 Visual Studio 来创建连接到 Azure SQL 数据库的 C# 程序，并使用 Transact-SQL 语句来查询数据。|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|此快速入门演示如何在 Windows/Linux/macOS 中使用 .NET Core 来创建连接到 Azure SQL 数据库的 C# 程序，并使用 Transact-SQL 语句来查询数据。|
|[Go](sql-database-connect-query-go.md)|本快速入门演示了如何使用 Go 连接到 Azure SQL 数据库。 此外演示了用于查询和修改数据的 Transact-SQL 语句。|
|[Java](sql-database-connect-query-java.md)|本快速入门演示了如何使用 Java 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句查询数据。|
|[Node.js](sql-database-connect-query-nodejs.md)|此快速入门演示如何使用 Node.js 来创建连接到 Azure SQL 数据库的程序，并使用 Transact-SQL 语句来查询数据。|
|[PHP](sql-database-connect-query-php.md)|此快速入门演示如何使用 PHP 来创建连接到 Azure SQL 数据库的程序，并使用 Transact-SQL 语句来查询数据。|
|[Python](sql-database-connect-query-python.md)|本快速入门演示了如何使用 Python 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句查询数据。 |
|[Ruby](sql-database-connect-query-ruby.md)|此快速入门演示如何使用 Ruby 来创建连接到 Azure SQL 数据库的程序，并使用 Transact-SQL 语句来查询数据。|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL 数据库连接的 TLS 注意事项
Microsoft 提供或支持的所有驱动程序使用传输层安全性 (TLS) 连接 Azure SQL 数据库。 无需特殊配置。 对于到 SQL Server 或 Azure SQL 数据库的所有连接，我们建议所有应用程序设置以下配置或其等效项：

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

某些系统对于这些配置关键字使用不同但等效的关键字。 这些配置可确保客户端驱动程序验证从服务器收到的 TLS 证书的标识。

如果需要符合支付卡行业 - 数据安全标准 (PCI-DSS)，我们还建议你在客户端上禁用 TLS 1.1 和 1.0。

默认情况下，非 Microsoft 驱动程序可能不会使用 TLS。 连接到 Azure SQL 数据库时，这可能是一个因素。 使用嵌入式驱动程序的应用程序可能不允许你控制这些连接设置。 我们建议你在与敏感数据进行交互的系统上使用此类驱动程序和应用程序之前，检查这些程序的安全性。

## <a name="libraries"></a>库

可以使用各种库和框架来连接到 Azure SQL 数据库。 查看[入门教程](https://aka.ms/sqldev)，快速开始使用 C#、Java、Node.js、PHP 和 Python 等编程语言。 然后使用 Linux、Windows 或 Docker（对于 macOS）上的 SQL Server 生成应用。

下表列出的连接库或驱动程序可供客户端应用程序使用各种语言进行连接并使用在本地或云中运行的 SQL Server。 可以在 Linux、Windows 或 Docker 上使用它们，并将其用于连接到 Azure SQL 数据库和 Azure SQL 数据仓库。 

| 语言 | 平台 | 其他资源 | 下载 | 入门 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [用于 SQL Server 的 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [下载](https://www.microsoft.com/net/download/) | [入门](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [Microsoft JDBC driver for SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [下载](https://go.microsoft.com/fwlink/?linkid=852460) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [用于 SQL Server 的 PHP SQL 驱动程序](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [下载](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [入门](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows、Linux、macOS | [用于 SQL Server 的 Node.js 驱动程序](https://msdn.microsoft.com/library/mt652093.aspx) | [安装](https://msdn.microsoft.com/library/mt652094.aspx) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驱动程序](https://msdn.microsoft.com/library/mt652092.aspx) | 安装选项： <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [入门](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [用于 SQL Server 的 Ruby 驱动程序](https://msdn.microsoft.com/library/mt691981.aspx) | [安装](https://msdn.microsoft.com/library/mt711041.aspx) | [入门](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [下载](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

下表列出了客户端应用程序可以将其与在本地或云中运行的 SQL Server 一起使用的对象关系映射 (ORM) 框架和 Web 框架的示例。 可以在 Linux、Windows 或 Docker 上使用这些框架，并将其用于连接到 SQL 数据库和 Azure SQL 数据仓库。 

| 语言 | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [实体框架](https://docs.microsoft.com/ef)<br>[实体框架核心](https://docs.microsoft.com/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows、Linux、macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>后续步骤

- 有关连接体系结构信息，请参阅 [Azure SQL 数据库连接体系结构](sql-database-connectivity-architecture.md)。
- 查找用来从客户端应用程序进行连接的 [SQL Server 驱动程序](https://msdn.microsoft.com/library/mt654049.aspx)
- 连接到 SQL 数据库：
  - [使用 .NET (C#) 连接到 SQL 数据库](sql-database-connect-query-dotnet.md) 
  - [使用 PHP 连接到 SQL 数据库](sql-database-connect-query-php.md) 
  - [使用 Node.js 连接到 SQL 数据库](sql-database-connect-query-nodejs.md) 
  - [使用 Java 连接到 SQL 数据库](sql-database-connect-query-java.md) 
  - [使用 Python 连接到 SQL 数据库](sql-database-connect-query-python.md)
  - [使用 Ruby 连接到 SQL 数据库](sql-database-connect-query-ruby.md)
- 重试逻辑代码示例：
  - [使用 ADO.NET 弹性连接到 SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [使用 PHP 弹性连接到 SQL][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
