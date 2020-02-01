---
title: 使用 Node.js 查询数据库
description: 如何使用 Node.js 创建连接到 Azure SQL 数据库的程序并使用 T-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768606"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>快速入门：使用 Node.js 查询 Azure SQL 数据库

在本快速入门中，我们使用 Node.js 连接到 Azure SQL 数据库，并使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个 [Azure SQL 数据库](sql-database-single-database-get-started.md)
- [Node.js](https://nodejs.org) 相关软件

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  执行 [Create Node.js apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)（使用 macOS 上的 SQL Server 创建 Node.js 应用）中的步骤 **1.2** 和 **1.3**，先安装 Homebrew 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  执行 [Create Node.js apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)（使用 Ubuntu 上的 SQL Server 创建 Node.js 应用）中的步骤 **1.2** 和 **1.3**，先安装 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  执行 [Create Node.js apps using SQL Server on Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)（使用 Windows 上的 SQL Server 创建 Node.js 应用）中的步骤 **1.2** 和 **1.3**，先安装 Chocolatey 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。

  ---

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 可以选择使用 Azure SQL 托管实例。
>
> 若要执行创建和配置操作，请使用 [Azure 门户](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然后设置[现场](sql-database-managed-instance-configure-p2s.md)或 [VM](sql-database-managed-instance-configure-vm.md) 连接性。
>
> 若要加载数据，请参阅[通过 BACPAC 进行还原](sql-database-import.md)（使用 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 文件），或参阅[还原 Wide World Importers 数据库](sql-database-managed-instance-get-started-restore.md)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 转到“SQL 数据库”或“SQL 托管实例”页。  

3. 在“概览”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称，或者托管实例的“主机”旁边的完全限定的服务器名称    。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标  。 

## <a name="create-the-project"></a>创建项目

打开命令提示符，然后创建一个名为 sqltest 的文件夹  。 打开已创建的文件夹，并运行以下命令：

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>添加用于查询数据库的代码

1. 在喜欢的文本编辑器中，创建新文件 *sqltest.js*。

1. 将其内容替换为以下代码。 然后，为服务器、数据库、用户和密码添加相应的值。

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> 代码示例使用适用于 Azure SQL 的 **AdventureWorksLT** 示例数据库。

## <a name="run-the-code"></a>运行代码

1. 在命令提示符下运行此程序。

    ```bash
    node sqltest.js
    ```

1. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## <a name="next-steps"></a>后续步骤

- [用于 SQL Server 的 Microsoft Node.js 驱动程序](/sql/connect/node-js/node-js-driver-for-sql-server)

- 通过 [.NET core](sql-database-connect-query-dotnet-core.md)、[Visual Studio Code](sql-database-connect-query-vscode.md) 或 [SSMS](sql-database-connect-query-ssms.md)（仅限 Windows）在 Windows/Linux/macOS 上进行连接和查询

- [在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)

- 使用 [.NET](sql-database-design-first-database-csharp.md) 或 [SSMS](sql-database-design-first-database.md) 设计第一个 Azure SQL 数据库
