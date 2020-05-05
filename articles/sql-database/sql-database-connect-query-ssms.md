---
title: SSMS：连接和查询数据
description: 了解如何通过使用 SQL Server Management Studio (SSMS) 连接到在 Azure 上的 SQL 数据库。 然后，通过运行 Transact-SQL (T-SQL) 语句来查询和编辑数据。
keywords: 连接到 sql 数据库, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79299288"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>快速入门：使用 SQL Server Management Studio 连接和查询 Azure SQL 数据库

本快速入门介绍如何使用 SQL Server Management Studio (SSMS) 连接到 Azure SQL 数据库并运行一些查询。

## <a name="prerequisites"></a>先决条件

完成本快速入门需要以下各项：

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/)。
- AdventureWorksLT 示例数据库。 如果需要 AdventureWorksLT 数据库的工作副本，完成[创建 Azure SQL 数据库](sql-database-single-database-get-started.md)快速入门即可创建一个。
    - 本文中脚本的编写目的是使用 AdventureWorksLT 数据库。 如果使用托管实例，则必须将 AdventureWorks 数据库导入一个实例数据库，或者修改本文中的脚本，以便使用 Wide World Importers 数据库。

如果只是想要在不安装 SSMS 的情况下运行某些即席查询，请参阅[快速入门：使用 Azure 门户的查询编辑器查询 SQL 数据库](sql-database-connect-query-portal.md)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到数据库所需的连接信息。 需要提供完全限定的服务器名称或主机名称、数据库名称和登录信息才能完成本快速入门。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 导航到要查询的“SQL 数据库”或“SQL 托管实例”。  

3. 在“概览”  页上，复制完全限定的服务器名称。 它位于单一数据库的“服务器名称”旁边，或者托管实例的“主机”旁边   。 完全限定的名称类似于 *servername.database.windows.net*，但使用你的实际服务器名称。

## <a name="connect-to-your-database"></a>连接到数据库

在 SSMS 中，连接到 Azure SQL 数据库服务器。

> [!IMPORTANT]
> Azure SQL 数据库服务器在端口 1433 上进行侦听。 若要从公司防火墙后连接到 SQL 数据库服务器，该防火墙必须打开此端口。

1. 打开 SSMS。

2. 此时会显示“连接到服务器”  对话框。 输入以下信息：

   | 设置      | 建议的值    | 说明 |
   | ------------ | ------------------ | ----------- |
   | **服务器类型** | 数据库引擎 | 所需的值。 |
   | **服务器名称** | 完全限定的服务器名称 | 类似于 **servername.database.windows.net**。 |
   | **身份验证** | SQL Server 身份验证 | 本教程使用 SQL 身份验证。 |
   | **登录** | 服务器管理员帐户用户 ID | 用于创建服务器的服务器管理员帐户的用户 ID。 |
   | **密码** | 服务器管理员帐户密码 | 用于创建服务器的服务器管理员帐户的密码。 |
   ||||

   ![连接到服务器](./media/sql-database-connect-query-ssms/connect.png)  

3. 选择“连接到服务器”对话框中的“选项”   。 在“连接到数据库”下拉菜单中，选择“mySampleDatabase”   。 完成[“先决条件”部分](#prerequisites)中的快速入门即可创建一个名为 mySampleDatabase 的 AdventureWorksLT 数据库。 如果 AdventureWorks 数据库的工作副本名称不同于 mySampleDatabase，请改为选择它。

   ![连接到服务器上的 DB](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. 选择“连接”  。 此时会打开“对象资源管理器”窗口。

5. 若要查看数据库的对象，请展开“数据库”，然后展开你的数据库节点  。

   ![mySampleDatabase 对象](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>查询数据

运行此 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 代码，按类别查询前 20 个产品。

1. 在对象资源管理器中，右键单击“mySampleDatabase”，并选择“新建查询”   。 此时会打开一个连接到数据库的新查询窗口。

2. 在查询窗口中粘贴以下 SQL 查询：

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 在工具栏上选择“执行”，以便运行查询并从 `Product` 和 `ProductCategory` 表检索数据  。

    ![从表 Product 和 ProductCategory 中检索数据的查询](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>插入数据

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

2. 选择“执行”在 `Product` 表中插入新行  。 “消息”面板显示“(受影响的 1 行)”   。

#### <a name="view-the-result"></a>查看结果

1. 将之前的查询替换为此查询。

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. 选择“执行”  。 将显示以下结果。

   ![Product 表查询的结果](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>更新数据

运行此 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 代码以修改新产品。

1. 将上一个查询替换为此查询，以便返回前面创建的新记录：

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 选择“执行”更新 `Product` 表中的指定行  。 “消息”面板显示“(受影响的 1 行)”   。

### <a name="delete-data"></a>删除数据

运行此 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 代码以删除新产品。

1. 将之前的查询替换为此查询。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 选择“执行”删除 `Product` 表中的指定行  。 “消息”面板显示“(受影响的 1 行)”   。

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
