---
title: SSMS：在 Azure SQL 数据库中连接和查询数据 | Microsoft Docs
description: 了解如何通过使用 SQL Server Management Studio (SSMS) 连接到在 Azure 上的 SQL 数据库。 然后，通过运行 Transact-SQL (T-SQL) 语句来查询和编辑数据。
keywords: 连接到 sql 数据库, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 23f2d32b2323821155467bd1ad12e9baf8c33074
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150744"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>快速入门：使用 SQL Server Management Studio 连接和查询 Azure SQL 数据库

在本快速入门中，将使用 [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) 连接到 Azure SQL 数据库。 然后，将运行 Transact-SQL 语句来查询、插入、更新和删除数据。 可以使用 SSMS 管理任何 SQL 基础结构（从适用于 Microsoft Windows 的 SQL Server 到 SQL 数据库，不一而足）。  

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-the-latest-ssms"></a>安装最新的 SSMS

在开始之前，请确保已安装最新的 [SSMS][ssms-install-latest-84g]。 

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="connect-to-your-database"></a>连接到数据库

在 SMSS 中，连接到 Azure SQL 数据库服务器。 

> [!IMPORTANT]
> Azure SQL 数据库逻辑服务器在端口 1433 上进行侦听。 要从公司防火墙后连接到逻辑服务器，该防火墙必须打开此端口。
>

1. 打开 SSMS。 此时会显示“连接到服务器”对话框。

2. 输入以下信息：

   | 设置      | 建议的值    | 说明 | 
   | ------------ | ------------------ | ----------- | 
   | **服务器类型** | 数据库引擎 | 所需的值。 |
   | **服务器名称** | 完全限定的服务器名称 | 类似于：mynewserver20170313.database.windows.net。 |
   | **身份验证** | SQL Server 身份验证 | 本教程使用 SQL 身份验证。 |
   | **登录名** | 服务器管理员帐户用户 ID | 用于创建服务器的服务器管理员帐户的用户 ID。 |
   | **密码** | 服务器管理员帐户密码 | 用于创建服务器的服务器管理员帐户的密码。 |
   ||||

   ![连接到服务器](./media/sql-database-connect-query-ssms/connect.png)  

3. 选择“连接到服务器”对话框中的“选项”。 在“连接到数据库”下拉菜单中，选择“mySampleDatabase”。

   ![连接到服务器上的 DB](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. 选择“连接”。 此时会打开“对象资源管理器”窗口。 

5. 要查看数据库的对象，请展开“数据库”，然后展开“mySampleDatabase”。

   ![查看数据库对象](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>查询数据

运行此 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 代码，按类别查询前 20 个产品。

1. 在对象资源管理器中，右键单击“mySampleDatabase”，并选择“新建查询”。 此时会打开一个连接到数据库的新查询窗口。

2. 在查询窗口中，粘贴此 SQL 查询。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 在工具栏上，选择“执行”，以便从 `Product` 和 `ProductCategory` 表检索数据。

    ![从两个表中检索数据的查询](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>插入数据

运行此 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 代码，在 `SalesLT.Product` 表中创建新产品。

1. 将之前的查询替换为此查询。

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. 选择“执行”在 `Product` 表中插入新行。 “消息”面板显示“(受影响的 1 行)”。

## <a name="view-the-result"></a>查看结果

1. 将之前的查询替换为此查询。

   ```sql
   SELECT * FROM [SalesLT].[Product] 
   WHERE Name='myNewProduct' 

2. Select **Execute**. The following result appears. 

   ![result](./media/sql-database-connect-query-ssms/result.png)

 
## Update data

Run this [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL code to modify your new product.

1. Replace the previous query with this one.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 选择“执行”更新 `Product` 表中的指定行。 “消息”面板显示“(受影响的 1 行)”。

## <a name="delete-data"></a>删除数据

运行此 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 代码以删除新产品。

1. 将之前的查询替换为此查询。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 选择“执行”删除 `Product` 表中的指定行。 “消息”面板显示“(受影响的 1 行)”。

## <a name="next-steps"></a>后续步骤

- 有关 SSMS 的信息，请参阅 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)。
- 若要使用 Azure 门户进行连接和查询，请参阅[使用 Azure 门户 SQL 查询编辑器进行连接和查询](sql-database-connect-query-portal.md)。
- 若要使用 Visual Studio Code 进行连接和查询，请参阅[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 进行连接和查询，请参阅[使用 PHP 进行连接和查询](sql-database-connect-query-php.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Python 进行连接和查询，请参阅[使用 Python 进行连接和查询](sql-database-connect-query-python.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。


<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

