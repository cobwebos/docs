---
title: "SSMS：在 Azure SQL 数据库中进行连接和数据查询 | Microsoft Docs"
description: "了解如何通过使用 SQL Server Management Studio (SSMS) 连接到在 Azure 上的 SQL 数据库。 然后，通过运行 Transact-SQL (T-SQL) 语句来查询和编辑数据。"
metacanonical: 
keywords: "连接到 sql 数据库, sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: fd72faf6c3a410242fd36a1abf4c556712c6beaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询

[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 是用于管理任何 SQL 基础结构（从适用于 Microsoft Windows 的 SQL Server 到 SQL 数据库，不一而足）的集成环境。 本快速入门演示了如何使用 SSMS 连接到 Azure SQL 数据库，并使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。 

## <a name="prerequisites"></a>先决条件

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)
- [创建 DB - PowerShell](sql-database-get-started-powershell.md)

在开始之前，请确保已安装最新版本的 [SSMS](https://msdn.microsoft.com/library/mt238290.aspx)。 

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，并单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。

   ![连接信息](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。 

## <a name="connect-to-your-database"></a>连接到数据库

使用 SQL Server Management Studio 建立到 Azure SQL 数据库服务器的连接。 

> [!IMPORTANT]
> Azure SQL 数据库逻辑服务器在端口 1433 上进行侦听。 如果尝试在企业防火墙内连接到 Azure SQL 数据库逻辑服务器，则必须在企业防火墙中打开此端口，否则无法成功进行连接。
>

1. 打开 SQL Server Management Studio。

2. 在“连接到服务器”对话框中，输入以下信息：

   | 设置       | 建议的值 | 说明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **服务器类型** | 数据库引擎 | 此值是必需的。 |
   | **服务器名称** | 完全限定的服务器名称 | 该名称应类似于：mynewserver20170313.database.windows.net。 |
   | **身份验证** | SQL Server 身份验证 | SQL 身份验证是本教程中配置的唯一身份验证类型。 |
   | **登录名** | 服务器管理员帐户 | 这是在创建服务器时指定的帐户。 |
   | **密码** | 服务器管理员帐户的密码 | 这是在创建服务器时指定的密码。 |

   ![连接到服务器](./media/sql-database-connect-query-ssms/connect.png)  

3. 单击“连接到服务器”对话框中的“选项”。 在“连接到数据库”部分输入 **mySampleDatabase**，以连接到此数据库。

   ![连接到服务器上的 DB](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. 单击“连接”。 此时会在 SSMS 中打开“对象资源管理器”窗口。 

   ![已连接到服务器](./media/sql-database-connect-query-ssms/connected.png)  

5. 在对象资源管理器中展开“数据库”，并展开 **mySampleDatabase**，查看示例数据库中的对象。

## <a name="query-data"></a>查询数据

通过以下代码使用 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 语句，以便按类别查询前 20 个产品。

1. 在“对象资源管理器”中，右键单击“mySampleDatabase”，并单击“新建查询”。 此时会打开一个空白查询窗口，该窗口连接到数据库。
2. 在查询窗口中输入以下查询：

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 在工具栏中，单击“执行”从 Product 表和 ProductCategory 表检索数据。

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>插入数据

通过以下代码使用 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 语句，将新产品插入到 SalesLT.Product 表中。

1. 在查询窗口中，以前的查询替换为以下查询：

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. 在工具栏上单击“执行”，将新行插入 Product 表。

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>更新数据

通过以下代码使用 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 语句，以便更新此前添加的新产品。

1. 在查询窗口中，以前的查询替换为以下查询：

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 在工具栏上单击“执行”，更新 Product 表中的指定行。

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>删除数据

通过以下代码使用 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句，以便删除此前添加的新产品。

1. 在查询窗口中，以前的查询替换为以下查询：

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 在工具栏上单击“执行”，删除 Product 表中的指定行。

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用 Transact-SQL 来创建和管理服务器和数据库，请参阅[了解 Azure SQL 数据库服务器和数据库](sql-database-servers-databases.md)。
- 有关 SSMS 的信息，请参阅[使用 SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)。
- 若要使用 Visual Studio Code 进行连接和查询，请参阅[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 进行连接和查询，请参阅[使用 PHP 进行连接和查询](sql-database-connect-query-php.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Python 进行连接和查询，请参阅[使用 Python 进行连接和查询](sql-database-connect-query-python.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。
