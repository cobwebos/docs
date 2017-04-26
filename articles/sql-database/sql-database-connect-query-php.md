---
title: "使用 PHP 连接 Azure SQL 数据库 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure SQL 数据库并进行查询的 PHP 代码示例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: e39d108e9d6962647cbf76e50299b73939fe5977
ms.lasthandoff: 04/19/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Azure SQL 数据库：使用 PHP 进行连接和数据查询

本快速入门演示了如何通过 Mac OS、Ubuntu Linux 和 Windows 平台使用 [PHP](http://php.net/manual/en/intro-whatis.php) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)

## <a name="install-php-and-database-communications-software"></a>安装 PHP 和数据库通信软件
### <a name="mac-os"></a>**Mac OS**
打开终端并输入以下命令，以安装 **brew**、**Microsoft ODBC Driver for Mac** 和 **Microsoft PHP Drivers for SQL Server**。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
输入以下命令以安装 **Microsoft ODBC Driver for Linux** 和 **Microsoft PHP Drivers for SQL Server**。

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- [从 Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx?lang=)安装 PHP 7.1.1 (x64) 
- 安装 [Microsoft ODBC 驱动程序 13.1](https://www.microsoft.com/download/details.aspx?id=53339)。 
- 下载[用于 SQL Server 的 Microsoft PHP 驱动程序](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows)的非线程安全 dll，并将二进制文件放在 PHP\v7.x\ext 文件夹中。
- 然后通过添加对 dll 的引用来编辑 php.ini (C:\Program Files\PHP\v7.1\php.ini) 文件。 例如：
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

此时，应已向 PHP 注册 dll。

## <a name="get-connection-information"></a>获取连接信息

在 Azure 门户中获取连接字符串。 请使用连接字符串连接到 Azure SQL 数据库。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。     
    
## <a name="select-data"></a>选择数据
请通过以下代码使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函数和 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 语句来查询 Azure SQL 数据库。 sqlsrv_query 函数可用于针对 SQL 数据库从查询中检索结果集。 此函数可接受任何查询，并返回可使用 [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php) 循环访问的结果集。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。 

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


## <a name="insert-data"></a>插入数据
请通过以下代码使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函数和 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 语句来将新产品插入到指定数据库中的 SalesLT.Product 表中。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。 

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
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>更新数据
请通过以下代码使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函数和 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 语句来更新 Azure SQL 数据库中的数据。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

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
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>删除数据
请通过以下代码使用 [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) 函数和 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 语句来删除 Azure SQL 数据库中的数据。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

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
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>后续步骤

- 有关 [Microsoft PHP Driver for SQL Server](https://github.com/Microsoft/msphpsql/) 的详细信息。
- [提出问题](https://github.com/Microsoft/msphpsql/issues)。
- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio 进行连接和查询，请参阅[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Python 进行连接和查询，请参阅[使用 Python 进行连接和查询](sql-database-connect-query-python.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。

