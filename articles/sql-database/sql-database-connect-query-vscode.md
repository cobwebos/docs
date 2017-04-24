---
title: "VS Code：在 Azure SQL 数据库中进行连接和数据查询 | Microsoft Docs"
description: "了解如何通过使用 Visual Studio Code 连接到 Azure 上的 SQL 数据库。 然后，通过运行 Transact-SQL (T-SQL) 语句来查询和编辑数据。"
metacanonical: 
keywords: "连接到 sql 数据库"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 5b623c78f8b8eac846c5ca244f1e0b25ee4f400f
ms.lasthandoff: 04/19/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Visual Studio Code 进行连接和数据查询

[Visual Studio Code](https://code.visualstudio.com/docs) 是一种图形代码编辑器，适用于 Linux、macOS 和 Windows，并且支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 SQL 数据仓库）。 本快速入门演示了如何使用 Visual Studio Code 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)

在开始之前，请确保已安装最新版 [Visual Studio Code](https://code.visualstudio.com/Download) 并加载 [mssql 扩展](https://aka.ms/mssql-marketplace)。 有关 mssql 扩展的安装指南，请参阅 [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code)（安装 VS Code）和 [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)（适用于 Visual Studio Code 的 mssql）。 

## <a name="configure-vs-code-mac-os-only"></a>配置 VS Code（仅 Mac OS）

### <a name="mac-os"></a>**Mac OS**
对于 macOS，需安装 OpenSSL，这是 mssql 扩展所使用的 DotNet Core 的先决条件。 打开终端并输入以下命令，以便安装 **brew** 和 **OpenSSL**。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

## <a name="get-connection-information"></a>获取连接信息

请在 Azure 门户中获取 Azure SQL 数据库服务器的完全限定服务器名称。 请使用 Visual Studio Code 通过完全限定的服务器名称连接到服务器。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。

   ![连接信息](./media/sql-database-connect-query-ssms/connection-information.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。 

## <a name="set-language-mode-to-sql"></a>将语言模式设置为 SQL

在 Visual Studio Code 中将语言模式设置为 **SQL**，以便启用 mssql 命令和 T-SQL IntelliSense。

1. 打开新的 Visual Studio Code 窗口。 

2. 单击状态栏右下角的“纯文本”。
3. 在打开的“选择语言模式”下拉列表中键入 **SQL**，然后按 **ENTER** 将语言模式设置为 SQL。 

   ![SQL 语言模式](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>连接到 SQL 数据库逻辑服务器中的数据库

使用 Visual Studio Code 建立到 Azure SQL 数据库服务器的连接。

> [!IMPORTANT]
> 在继续之前，请确保服务器、数据库和登录信息已准备就绪。 在开始输入连接配置文件信息的情况下，如果在 Visual Studio Code 中更改焦点，则需重新开始创建连接配置文件。
>

1. 在 VS Code 中，按 **CTRL+SHIFT+P**（或 **F1**）打开命令面板。

2. 键入 **sqlcon** 并按 **ENTER**。

3. 按 **ENTER** 选择“创建连接配置文件”。 这将为 SQL Server 实例创建连接配置文件。

4. 按照提示为新的连接配置文件指定连接属性。 指定每个值后，按 **ENTER** 继续。 

   下表介绍了连接配置文件的属性。

   | 设置 | 说明 |
   |-----|-----|
   | **服务器名称** | 输入完全限定的服务器名称，例如 **mynewserver20170313.database.windows.net** |
   | **数据库名称** | 输入数据库名称，例如 **mySampleDatabase** |
   | **身份验证** | 选择 SQL 登录名 |
   | **用户名** | 输入服务器管理员帐户 |
   | **密码(SQL 登录名)** | 输入服务器管理员帐户的密码 | 
   | **保存密码?** | 选择“是”或“否” |
   | **[可选] 输入此配置文件的名称** | 输入连接配置文件名称，例如 **mySampleDatabase**。 

5. 按 **ESC** 键关闭信息消息，该消息通知你，配置文件已创建并连接。

6. 验证状态栏中的连接。

   ![连接状态](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>查询数据

使用 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 语句查询 Azure SQL 数据库中的数据。

1. 在“编辑器”窗口的空查询窗口中，输入以下查询：

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. 按 **CTRL+SHIFT+E** 从 Product 表和 ProductCategory 表检索数据。

    ![查询](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>插入数据

使用 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 语句将数据插入 Azure SQL 数据库。

1. 在“编辑器”窗口中删除以前的查询，然后输入以下查询：

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

2. 按 **CTRL+SHIFT+E** 在 Product 表中插入新行。

## <a name="update-data"></a>更新数据

使用 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 语句更新 Azure SQL 数据库中的数据。

1.  在“编辑器”窗口中删除以前的查询，然后输入以下查询：

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 按 **CTRL+SHIFT+E** 更新 Product 表中的指定行。

## <a name="delete-data"></a>删除数据

使用 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句删除 Azure SQL 数据库中的数据。

1. 在“编辑器”窗口中删除以前的查询，然后输入以下查询：

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 按 **CTRL+SHIFT+E** 删除 Product 表中的指定行。

## <a name="next-steps"></a>后续步骤

- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 进行连接和查询，请参阅[使用 PHP 进行连接和查询](sql-database-connect-query-php.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Python 进行连接和查询，请参阅[使用 Python 进行连接和查询](sql-database-connect-query-python.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。

