---
title: 使用 Node.js 查询 Azure SQL 数据库 | Microsoft Docs
description: 如何使用 Node.js 创建连接到 Azure SQL 数据库的程序并使用 T-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 22c783574897076b697b4b2bb3e8fc1da284dcae
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724666"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>快速入门：使用 Node.js 查询 Azure SQL 数据库

本文演示了如何使用 [Node.js](https://nodejs.org) 连接到 Azure SQL 数据库。 然后即可使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成此示例，请确保具备以下先决条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 针对所用计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal-firewall.md)

- 适用于操作系统的 Node.js 相关软件：

  - **MacOS**：安装 Homebrew 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)。
  
  - **Ubuntu**：安装 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)。
  
  - **Windows**：安装 Chocolatey 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)。

## <a name="get-database-connection"></a>获取数据库连接

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> 对于在其上执行本教程操作的计算机，必须为其公共 IP 地址制定防火墙规则。 如果使用其他计算机或其他公共 IP 地址，则[使用 Azure 门户创建服务器级防火墙规则](sql-database-get-started-portal-firewall.md)。

## <a name="create-the-project"></a>创建项目

打开命令提示符，然后创建一个名为 sqltest 的文件夹。 导航到已创建的文件夹，并运行以下命令：

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>添加用于查询数据库的代码

1. 在喜欢的文本编辑器中，创建新文件 *sqltest.js*。

1. 将其内容替换为以下代码。 然后，为服务器、数据库、用户和密码添加相应的值。

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
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
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        var request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
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
