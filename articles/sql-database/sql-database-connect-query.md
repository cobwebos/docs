---
title: Azure SQL 数据库连接和查询快速入门 | Microsoft Docs
description: 演示如何连接和查询 Azure SQL 数据库的 Azure SQL 数据库快速入门。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc
ms.devlang: ''
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: carlrab
ms.openlocfilehash: ec39c5ad0771c2bc78655e52c58949db6e9b3353
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186005"
---
# <a name="azure-sql-database-connect-and-query-quickstarts"></a>Azure SQL 数据库连接和查询快速入门

以下文档包含演示如何连接和查询 Azure SQL 数据库的 Azure 示例链接。 它还针对传输级别安全性提供了一些建议。

## <a name="quickstarts"></a>快速入门

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|本快速入门演示了如何使用 SSMS 连接到 Azure SQL 数据库，并使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。|
|[SQL Operations Studio ](https://docs.microsoft.com/sql/sql-operations-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|此快速入门演示如何使用 SQL Operations Studio（预览版）连接到 Azure SQL 数据库，然后使用 Transact-SQL (T-SQL) 语句创建要在 SQL Operations Studio（预览版）教程中使用的 TutorialDB。|
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

## <a name="next-steps"></a>后续步骤

有关连接体系结构信息，请参阅 [Azure SQL 数据库连接体系结构](sql-database-connectivity-architecture.md)。