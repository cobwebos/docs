---
title: 使用 PHP 进行查询
description: 介绍如何使用 PHP 创建连接到 Azure SQL 数据库中数据库或 Azure SQL 托管实例的程序，并使用 T-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2 
ms.openlocfilehash: e8b9f12c57b2e4bf943e5fc9a9ebc2bbadef9ee9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "86504966"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>快速入门：使用 PHP 查询 Azure SQL 数据库中的数据库
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文演示了如何使用 [PHP](https://php.net/manual/en/intro-whatis.php) 连接到 Azure SQL 数据库或 Azure SQL 托管实例中的数据库。 然后即可使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure SQL 数据库或 Azure SQL 托管实例中的数据库。 可以根据下述快速入门之一，创建数据库，然后对其进行配置：

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




  > [!IMPORTANT]
  > 本文中脚本的编写目的是使用 Adventure Works 数据库。 使用 SQL 托管实例时，必须将 Adventure Works 数据库导入一个实例数据库，或者修改本文中的脚本，以便使用 Wide World Importers 数据库。

- 已为操作系统安装与 PHP 相关的软件：

  - **MacOS**，安装 PHP、ODBC 驱动程序，然后安装 PHP Driver for SQL Server。 请参阅[步骤 1、2 和 3](/sql/connect/php/installation-tutorial-linux-mac)。

  - **Linux**，安装 PHP、ODBC 驱动程序，然后安装 PHP Driver for SQL Server。 请参阅[步骤 1、2 和 3](/sql/connect/php/installation-tutorial-linux-mac)。

  - **Windows**：安装用于 IIS Express 的 PHP 和 Chocolatey，然后安装 ODBC 驱动程序和 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/)。

## <a name="get-server-connection-information"></a>获取服务器连接信息

获取连接到 Azure SQL 数据库中的数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 导航到“SQL 数据库”或“SQL 托管实例”页 。

3. 在“概述”页上，在“Server 名称”旁查看 Azure SQL 数据库中的数据库的完全限定服务器名称，或在“Host”旁边查看 Azure VM 上的 Azure SQL 托管实例或 SQL Server 的完全限定服务器名称（或 IP 地址）  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!NOTE]
> 有关 Azure VM 上的 SQL Server 的连接信息，请参阅[连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="add-code-to-query-the-database"></a>添加用于查询数据库的代码

1. 在喜欢的文本编辑器中，创建新文件 sqltest.php。  

1. 将其内容替换为以下代码。 然后，为服务器、数据库、用户和密码添加相应的值。

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>运行代码

1. 在命令提示符下运行此应用。

   ```bash
   php sqltest.php
   ```

1. 验证是否返回了前 20 行，然后关闭应用窗口。

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL 数据库中设计第一个数据库](design-first-database-tutorial.md)
- [用于 SQL Server 的 Microsoft PHP 驱动程序](https://github.com/Microsoft/msphpsql/)
- [报告问题或提出问题](https://github.com/Microsoft/msphpsql/issues)
- [重试逻辑示例：使用 PHP 弹性连接到 Azure SQL](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
