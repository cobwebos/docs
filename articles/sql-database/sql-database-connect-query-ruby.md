---
title: "使用 Ruby 连接 Azure SQL 数据库 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure SQL 数据库并进行查询的 Ruby 代码示例。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2d1092af00c1bce3e47d980a512a25eff1f0d9b6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Ruby 进行连接和数据查询

本快速入门演示了如何通过 Mac OS 和 Ubuntu Linux 平台使用 [Ruby](https://Ruby.org) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。

## <a name="prerequisites"></a>先决条件

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)
- [创建 DB - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>安装 Ruby 和数据库通信库

本部分中的步骤假定你熟悉使用 Ruby 开发，但不熟悉如何使用 Azure SQL 数据库。 如果你不熟悉如何使用 Ruby 进行开发，请转到[使用 SQL Server 生成应用](https://www.microsoft.com/en-us/sql-server/developer-get-started/)并选择 **Ruby**，然后选择操作系统。

### <a name="mac-os"></a>**Mac OS**
打开终端并导航到要在其中创建 Ruby 脚本的目录。 输入以下命令以安装 **brew**、**FreeTDS** 和 **TinyTDS**。

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
打开终端并导航到要在其中创建 Ruby 脚本的目录。 输入以下命令以安装 **FreeTDS** 和 **TinyTDS**。

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>获取连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。
    

## <a name="select-data"></a>选择数据
通过以下代码将 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数与 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 语句配合使用来按类别查询前 20 个产品。 TinyTDS::Client 函数接受查询并返回结果集。 可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds) 循环访问结果集。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
通过以下代码将 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数与 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 语句配合使用来将新产品插入到 SalesLT.Product 表中。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

本示例介绍了如何安全地执行 INSERT 语句，传递参数以保护应用程序免遭 [SQL 注入](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx)漏洞的危害，然后检索自动生成的[主键](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints)值。    
  
若要配合使用 TinyTDS 和 Azure，建议运行多个 `SET` 语句来更改当前会话处理特定信息的方式。 建议使用代码示例中所提供的 `SET` 语句。 例如，即使未显式指定列的可为 null 状态，通过 `SET ANSI_NULL_DFLT_ON` 依然可以创建新列来允许 null 值。  
  
为符合 Microsoft SQL Server [日期时间](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql)格式，请使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函数转换成对应的日期时间格式。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
通过以下代码将 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数与 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 语句配合使用来更新之前添加的新产品。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
通过以下代码将 [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 函数与 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 语句配合使用来删除之前添加的新产品。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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
- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [TinyTDS 的 GitHub 存储库](https://github.com/rails-sqlserver/tiny_tds)
- [报告问题/提出问题](https://github.com/rails-sqlserver/tiny_tds/issues)
- [用于 SQL Server 的 Ruby 驱动程序](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


