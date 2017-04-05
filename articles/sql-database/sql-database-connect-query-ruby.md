---
title: "使用 Ruby 连接 Azure SQL 数据库 | Microsoft Docs"
description: "使用本快速入门教程中的示例代码可以生成一个包含 Ruby 的新型应用程序，该应用程序受云中强大的 Azure SQL 数据库关系数据库的支持。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 03/28/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8e01a717cbef6b5f6d26a8191c44e249bf7a9567
ms.lasthandoff: 03/30/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Ruby 进行连接和数据查询

使用 [Ruby](https://Ruby.org) 连接 Azure SQL 数据库并进行查询。 本快速入门教程详述了如何使用 Ruby 连接到 Azure SQL 数据库，然后执行查询、插入、更新和删除语句。

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>配置开发环境

下文将详细介绍如何配置现有 Mac OS 和 Linux(Ubuntu) 开发环境，以便使用 Azure SQL 数据库。

### <a name="mac-os"></a>**Mac OS**
打开终端并导航到要在其中创建 Ruby 脚本的目录。 输入以下命令以安装 **brew**、**FreeTDS** 和 **TinyTDS**。

```ruby
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
打开终端并导航到要在其中创建 Ruby 脚本的目录。 输入以下命令以安装 **FreeTDS** 和 **TinyTDS**。

```ruby
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>获取连接信息

在 Azure 门户中获取连接字符串。 请使用连接字符串连接到 Azure SQL 数据库。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概要”窗格中，查看完全限定的服务器名称。

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
    

## <a name="select-data"></a>选择数据
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数和 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 语句查询 Azure SQL 数据库中的数据。 TinyTDS::Client 函数接受查询并返回结果集。 可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) 循环访问结果集。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>插入数据
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数和 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 语句将数据插入 Azure SQL 数据库。

本示例介绍如何安全地执行 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) 语句，传递参数以保护应用程序免遭 [SQL 注入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx)漏洞的危害，然后检索自动生成的[主键](https://msdn.microsoft.com/library/ms179610.aspx)值。    
  
若要配合使用 TinyTDS 和 Azure，建议运行多个 `SET` 语句来更改当前会话处理特定信息的方式。 建议使用代码示例中所提供的 `SET` 语句。 例如，即使未显式指定列的可为 null 状态，通过 `SET ANSI_NULL_DFLT_ON` 依然可以创建新列来允许 null 值。  
  
为符合 Microsoft SQL Server [日期时间](http://msdn.microsoft.com/library/ms187819.aspx)格式，请使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函数转换成对应的日期时间格式。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON"")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>更新数据
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数和 [UPDATE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句删除 Azure SQL 数据库中的数据。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>删除数据
使用 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数和 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句删除 Azure SQL 数据库中的数据。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>后续步骤
* 请参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)。
* 有关 [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/) 的详细信息。
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。

