---
title: 使用 Node.js 查询数据库
description: 介绍如何使用 Node.js 创建连接到 Azure SQL 数据库中数据库或 Azure SQL 托管实例的程序，并使用 T-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2 
ms.openlocfilehash: 7135b3630d7b780d2c5d18f22b2a05ee6aedf8ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504525"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：使用 Node.js 查询 Azure SQL 数据库中的数据库或 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本快速入门将使用 Node.js 连接到数据库并查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

  | 操作 | SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 创建| [门户](single-database-create-quickstart.md) | [门户](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 配置 | [服务器级别 IP 防火墙规则](firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)|
  |||[来自本地的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](database-import.md) 文件还原或导入 Adventure Works| 从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](database-import.md) 文件还原或导入 Adventure Works|
  |||


- [Node.js](https://nodejs.org) 相关软件

  # <a name="macos"></a>[macOS](#tab/macos)

  执行 [使用 macOS 上的 SQL Server 创建 Node.js 应用](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)中的步骤 1.2 和 1.3，先安装 Homebrew 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD 。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  执行 [使用 Ubuntu 上的 SQL Server 创建 Node.js 应用](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)中的步骤 1.2 和 1.3，先安装 Node.js，然后安装 ODBC 驱动程序和 SQLCMD 。

  # <a name="windows"></a>[Windows](#tab/windows)

  执行 [使用 Windows 上的 SQL Server 创建 Node.js 应用](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)中的步骤 1.2 和 1.3，先安装 Chocolatey 和 Node.js，然后安装 ODBC 驱动程序和 SQLCMD 。

  ---

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 可以选择使用 Azure SQL 托管实例。
>
> 若要执行创建和配置操作，请使用 [Azure 门户](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然后设置[本地](../managed-instance/point-to-site-p2s-configure.md)或 [VM](../managed-instance/connect-vm-instance-configure.md) 连接性。
>
> 若要加载数据，请参阅[通过 BACPAC 进行还原](database-import.md)（使用 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 文件），或参阅[还原 Wide World Importers 数据库](../managed-instance/restore-sample-database-quickstart.md)。

## <a name="get-server-connection-information"></a>获取服务器连接信息

获取连接到 Azure SQL 数据库中的数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 转到“SQL 数据库”或“SQL 托管实例”页 。

3. 在“概述”页上，在“Server 名称”旁查看 Azure SQL 数据库中的数据库的完全限定服务器名称，或在“Host”旁边查看 Azure VM 上的 Azure SQL 托管实例或 SQL Server 的完全限定服务器名称（或 IP 地址）  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!NOTE]
> 有关 Azure VM 上的 SQL Server 的连接信息，请参阅[连接到 SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="create-the-project"></a>创建项目

打开命令提示符，然后创建一个名为 sqltest 的文件夹。 打开已创建的文件夹，并运行以下命令：

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>添加用于查询数据库的代码

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
> 代码示例使用 Azure SQL 数据库中的 AdventureWorksLT 示例数据库。

## <a name="run-the-code"></a>运行代码

1. 在命令提示符下运行此程序。

    ```bash
    node sqltest.js
    ```

1. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## <a name="next-steps"></a>后续步骤

- [用于 SQL Server 的 Microsoft Node.js 驱动程序](/sql/connect/node-js/node-js-driver-for-sql-server)

- 通过 [.NET core](connect-query-dotnet-core.md)、[Visual Studio Code](connect-query-vscode.md) 或 [SSMS](connect-query-ssms.md)（仅限 Windows）在 Windows/Linux/macOS 上进行连接和查询

- [在 Windows/Linux/macOS 中通过命令行使用 .NET Core 入门](/dotnet/core/tutorials/using-with-xplat-cli)

- 使用 [.NET](design-first-database-csharp-tutorial.md) 或 [SSMS](design-first-database-tutorial.md) 在 Azure SQL 数据库中设计第一个数据库
