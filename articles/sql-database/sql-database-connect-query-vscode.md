---
title: VS Code：在 Azure SQL 数据库中连接和查询数据 | Microsoft Docs
description: 了解如何通过使用 Visual Studio Code 连接到 Azure 上的 SQL 数据库。 然后，通过运行 Transact-SQL (T-SQL) 语句来查询和编辑数据。
keywords: 连接到 sql 数据库
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: 786b4fab42fb49cd44c1985bfa04ce33e77cadf0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098978"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>快速入门：使用 Visual Studio Code 连接和查询 Azure SQL 数据库

[Visual Studio Code](https://code.visualstudio.com/docs) 是一种图形代码编辑器，适用于 Linux、macOS 和 Windows。 它支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 SQL 数据仓库）。 本快速入门演示如何使用 Visual Studio Code 连接到 Azure SQL 数据库，然后运行 Transact-SQL 语句以查询、插入、更新和删除数据。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-visual-studio-code"></a>安装 Visual Studio Code

请确保已安装最新版 [Visual Studio Code](https://code.visualstudio.com/Download) 并加载了 [mssql 扩展](https://aka.ms/mssql-marketplace)。 有关 mssql 扩展的安装指南，请参阅[安装 VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) 和 [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)（适用于 Visual Studio Code 的 mssql）。 

## <a name="configure-visual-studio-code"></a>配置 Visual Studio Code 

### <a name="mac-os"></a>**Mac OS**
对于 macOS，需安装 OpenSSL，这是 mssql 扩展所使用的 .Net Core 的先决条件。 打开终端并输入以下命令，以便安装 **brew** 和 **OpenSSL**。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

无需特殊配置。

### <a name="windows"></a>**Windows**

无需特殊配置。

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>将语言模式设置为 SQL

在 Visual Studio Code 中，将语言模式设置为 SQL，以便启用 mssql 命令和 T-SQL IntelliSense。

1. 打开新的 Visual Studio Code 窗口。 

2. 按 CTRL+N。 这会打开一个新的纯文本文件。 

3. 选择状态栏右下角的“纯文本”。

4. 在打开的“选择语言模式”下拉菜单中，选择“SQL”。 

## <a name="connect-to-your-database"></a>连接到数据库

使用 Visual Studio Code 建立到 Azure SQL 数据库服务器的连接。

> [!IMPORTANT]
> 在继续之前，请确保服务器和登录信息已准备就绪。 在开始输入连接配置文件信息的情况下，如果在 Visual Studio Code 中更改焦点，则需重新开始创建配置文件。
>

1. 在 Visual Studio Code 中，按 Ctrl+Shift+P（或 F1）打开命令面板。

2. 选择“MS SQL:Connect”，然后按 Enter。

3. 选择“创建连接配置文件”。

4. 按照提示指定新配置文件的连接属性。 指定每个值后，按 Enter 继续。 

   | 属性       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **服务器名称** | 完全限定的服务器名称 | 类似于：mynewserver20170313.database.windows.net。 |
   | **数据库名称** | mySampleDatabase | 要连接到的数据库。 |
   | **身份验证** | SQL 登录名| 本教程使用 SQL 身份验证。 |
   | **用户名** | 用户名 | 用于创建服务器的服务器管理员帐户的用户名。 |
   | **密码(SQL 登录名)** | 密码 | 用于创建服务器的服务器管理员帐户的密码。 |
   | **保存密码?** | 是或否 | 如果不希望每次都输入密码，则请选择“是”。 |
   | 输入此配置文件的名称 | 配置文件名称，例如 mySampleProfile | 保存配置文件可以在后续登录时加快连接速度。 | 

   如果成功，会显示通知，指出已创建并连接配置文件。

## <a name="query-data"></a>查询数据

使用以下 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 语句，按类别查询排名前 20 的产品。

1. 在编辑器窗口中，粘贴以下 SQL 查询。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. 按 Ctrl+Shift+E 执行查询并显示来自 `Product` 和 `ProductCategory` 表的结果。

    ![从 2 个表中检索数据的查询](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>插入数据

使用以下 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 语句，将新产品添加到 `SalesLT.Product` 表中。

1. 将之前的查询替换为此查询。

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

2. 按 Ctrl+Shift+E 在 `Product` 表中插入新行。

## <a name="update-data"></a>更新数据

使用以下 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 语句更新已添加的产品。

1. 将之前的查询替换为此查询：

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 按 Ctrl+Shift+E 更新 `Product` 表中的指定行。

## <a name="delete-data"></a>删除数据

使用以下 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 语句删除新产品。

1. 将之前的查询替换为此查询：

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 按 Ctrl+Shift+E 删除 `Product` 表中的指定行。

## <a name="next-steps"></a>后续步骤

- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[快速入门：使用 SQL Server Management Studio 连接到 Azure SQL 数据库并查询数据](sql-database-connect-query-ssms.md)。
- 若要使用 Azure 门户进行连接和查询，请参阅[快速入门：使用 Azure 门户中的 SQL 查询编辑器进行连接并查询数据](sql-database-connect-query-portal.md)。
- 有关使用 Visual Studio Code 的 MSDN 杂志文章，请参阅[“利用 MSSQL 扩展创建数据库 IDE”博客文章](https://msdn.microsoft.com/magazine/mt809115)。
