---
title: "使用 PHP 查询 Azure SQL 数据库 | Microsoft Docs"
description: "本主题介绍如何使用 PHP 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a 22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 08/08/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3a43472ad2be4a0fd6f7126f72433acd8b5f25fd
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="use-php-to-query-an-azure-sql-database"></a>使用 PHP 查询 Azure SQL 数据库

本快速入门教程演示了如何使用 [PHP](http://php.net/manual/en/intro-whatis.php) 来创建连接到 Azure SQL 数据库的程序，并使用 Transact-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门教程，请确保符合以下条件：

- Azure SQL 数据库。 此快速入门使用以下某个快速入门中创建的资源： 

   - [创建 DB - 门户](sql-database-get-started-portal.md)
   - [创建 DB - CLI](sql-database-get-started-cli.md)
   - [创建 DB - PowerShell](sql-database-get-started-powershell.md)

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 已为操作系统安装 PHP 和相关软件。

    - **MacOS**：安装 Homebrew 和 PHP，接着安装 ODBC 驱动程序和 SQLCMD，再安装 PHP Driver for SQL Server。 请参阅[步骤 1.2、1.3 和 2.1](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/mac/)。
    - **Ubuntu**：安装 PHP 和其他所需包，然后安装 PHP Driver for SQL Server。 请参阅[步骤 1.2 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)。
    - **Windows**：安装最新版的 PHP for IIS Express、最新版的 Microsoft Drivers for SQL Server（位于 IIS Express 中）、Chocolatey、ODBC 驱动程序以及 SQLCMD。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/)。    

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，并单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。     
    
## <a name="insert-code-to-query-sql-database"></a>插入用于查询 SQL 数据库的代码

1. 在喜欢的文本编辑器中，创建新文件 sqltest.php。  

2. 将内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
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

1. 请在命令提示符处运行以下命令：

   ```php
   php sqltest.php
   ```

2. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## <a name="next-steps"></a>后续步骤
- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [用于 SQL Server 的 Microsoft PHP 驱动程序](https://github.com/Microsoft/msphpsql/)
- [报告问题或提出问题](https://github.com/Microsoft/msphpsql/issues)

