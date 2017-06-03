---
title: "使用 Node.js 连接 Azure SQL 数据库 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure SQL 数据库并进行查询的 Node.js 代码示例。"
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4d3c3541749870b09aecc9efb63413f7c045e044
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Node.js 进行连接和数据查询

本快速入门演示了如何通过 Windows、Ubuntu Linux 和 Mac 平台使用 [Node.js](https://nodejs.org/en/) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。

## <a name="prerequisites"></a>先决条件

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)
- [创建 DB - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>安装 Node.js 

本部分中的步骤假定你熟悉使用 Node.js 开发，但不熟悉如何使用 Azure SQL 数据库。 如果你不熟悉如何使用 Node.js 进行开发，请转到[使用 SQL Server 生成应用](https://www.microsoft.com/en-us/sql-server/developer-get-started/)并选择 **Node.js**，然后选择操作系统。

### <a name="mac-os"></a>**Mac OS**
输入以下命令安装 **brew**（适用于 Mac OS X 和 **Node.js** 的易用程序包管理器）。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
输入以下命令安装 **Node.js** 和 **npm**（适用于 Node.js 的程序包管理器）。

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
请访问 [Node.js 下载页](https://nodejs.org/en/download/)并选择所需的 Windows 安装程序选项。


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>安装适用于 Node.js 的 Tedious SQL Server 驱动程序
适用于 Node.js 的推荐驱动程序是 **[tedious](https://github.com/tediousjs/tedious)**。 Tedious 是 Microsoft 推出的一个开源协议，适用于任何平台上的 Node.js 应用程序。 在本教程中，你需要一个空的目录，以便存放代码和即将安装的 `npm` 依赖项。

若要安装 **tedious** 驱动程序，请在目录中运行以下命令：

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>获取连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。
    
## <a name="select-data"></a>选择数据

使用以下代码在 Azure SQL 数据库中按类别查询前 20 个产品。 首先从 tedious 驱动程序库中导入驱动程序连接和请求类。 之后，创建配置对象，并将 **username**、**password**、**server** 和 **database** 变量替换为之前使用 AdventureWorksLT 示例数据创建数据库时指定的值。 使用指定的 `config` 对象创建 `Connection` 对象。 之后，定义 `connection` 对象的 `connect` 事件的回调，以执行 `queryDatabase()` 函数。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>将数据插入数据库
通过以下代码使用 `insertIntoDatabase()` 函数和 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 语句来将新产品插入到 SalesLT.Product 表中。 将 **username**、**password**、**server** 和 **database** 替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>更新数据库中的数据
通过以下代码使用 `updateInDatabase()` 函数和 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 语句来删除之前添加的新产品。 将 **username**、**password**、**server** 和 **database** 替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。 此示例中使用了在上一示例中插入的产品名称。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>从数据库删除数据
使用以下代码从数据库删除数据。 将 **username**、**password**、**server** 和 **database** 替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。 此时，在 `deleteFromDatabase()` 函数中使用 **DELETE 语句**。 此示例中使用的也是在上一个示例中插入的产品名称。

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>后续步骤
- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [用于 SQL Server 的 Microsoft Node.js 驱动程序](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- [使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。



