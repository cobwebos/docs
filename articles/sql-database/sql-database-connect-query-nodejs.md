---
title: "使用 Node.js 查询 Azure SQL 数据库 | Microsoft Docs"
description: "本主题介绍如何使用 Node.js 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 56696c6c85f117449e7fb80b0daca707267d41c1
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# 使用 Node.js 查询 Azure SQL 数据库
<a id="use-nodejs-to-query-an-azure-sql-database" class="xliff"></a>

本快速入门教程演示了如何使用 [Node.js](https://nodejs.org/en/) 来创建连接到 Azure SQL 数据库的程序，并使用 Transact-SQL 语句来查询数据。

## 先决条件
<a id="prerequisites" class="xliff"></a>

若要完成本快速入门教程，请确保符合以下条件：

- Azure SQL 数据库。 此快速入门使用以下某个快速入门中创建的资源： 

   - [创建 DB - 门户](sql-database-get-started-portal.md)
   - [创建 DB - CLI](sql-database-get-started-cli.md)
   - [创建 DB - PowerShell](sql-database-get-started-powershell.md)

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。
- 已为操作系统安装 Node.js 和相关软件。
    - **MacOS**：安装 Homebrew 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)。
    - **Ubuntu**：安装 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)。
    - **Windows**：安装 Chocolatey 和 Node.js，然后安装 ODBC 驱动程序和 SQL CMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)。

## SQL Server 连接信息
<a id="sql-server-connection-information" class="xliff"></a>

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。

> [!IMPORTANT]
> 对于在其上执行本教程操作的计算机，必须为其公共 IP 地址制定防火墙规则。 如果使用其他计算机或其他公共 IP 地址，则[使用 Azure 门户创建服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。 

## 创建 Node.js 项目
<a id="create-a-nodejs-project" class="xliff"></a>

打开命令提示符，然后创建一个名为 sqltest 的文件夹。 导航到已创建的文件夹，并运行以下命令：

    
    npm init -y
    npm install tedious
    npm install async
    

## 插入用于查询 SQL 数据库的代码
<a id="insert-code-to-query-sql-database" class="xliff"></a>

1. 在开发环境或喜欢的文本编辑器中，创建新文件 sqltest.js。

2. 将内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
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

## 运行代码
<a id="run-the-code" class="xliff"></a>

1. 请在命令提示符处运行以下命令：

   ```js
   node sqltest.js
   ```

2. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## 后续步骤
<a id="next-steps" class="xliff"></a>

- 了解[用于 SQL Server 的 Microsoft Node.js 驱动程序](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- 了解如何在 Windows/Linux/macOS 中[使用 .NET Core 连接和查询 Azure SQL 数据库](sql-database-connect-query-dotnet-core.md)。  
- 了解[在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli.md)。
- 了解如何[使用 SSMS 设计你的第一个 Azure SQL 数据库](sql-database-design-first-database.md)，或者如何[使用 .NET 设计你的第一个 Azure SQL 数据库](sql-database-design-first-database-csharp.md)。
- 了解如何[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 了解如何[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。



