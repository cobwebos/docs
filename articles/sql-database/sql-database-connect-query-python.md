---
title: "使用 Python 连接 Azure SQL 数据库 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure SQL 数据库并进行查询的 Python 代码示例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: e75058c8b387bc090bf924b9099a64e5d154afa4
ms.lasthandoff: 04/19/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Python 进行连接和数据查询

 本快速入门演示了如何通过 Mac OS、Ubuntu Linux 和 Windows 平台使用 [Python](https://python.org) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句在数据库中查询、插入、更新和删除数据。

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)

## <a name="install-the-python-and-database-communication-libraries"></a>安装 Python 和数据库通信库

### <a name="mac-os"></a>**Mac OS**
打开终端并导航到你要在其中创建 python 脚本的目录。 输入以下命令，安装 **brew**、**Microsoft ODBC Driver for Mac**和 **pyodbc**。 pyodbc 使用 Linux 上的 Microsoft ODBC 驱动程序连接到 SQL 数据库。

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
打开终端并导航到你要在其中创建 python 脚本的目录。 输入以下命令以安装**适用于 Linux 的 Microsoft ODBC 驱动程序**和 **pyodbc**。 pyodbc 使用 Linux 上的 Microsoft ODBC 驱动程序连接到 SQL 数据库。

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
安装 [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339)（如果系统提示，则升级驱动程序）。 Pyodbc 使用 Linux 上的 Microsoft ODBC 驱动程序连接到 SQL 数据库。 

然后使用 pip 安装 **pyodbc**。

```cmd
pip install pyodbc==3.1.1
```

可在[此处](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到有关使用 pip 的说明

## <a name="get-connection-information"></a>获取连接信息

使用以下步骤中的信息获取 Azure SQL 数据库服务器和数据库的连接信息。 需要通过此信息使用 Python 连接 Azure SQL 数据库并进行查询。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了 Azure SQL 数据库服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。     
   
## <a name="select-data"></a>选择数据

请通过以下代码使用 [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) 函数和 [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL 语句来查询 Azure SQL 数据库。 [Cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数可用于针对 SQL 数据库从查询中检索结果集。 此函数可接受任何查询，并返回可使用 [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html) 循环访问的结果集。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>插入数据
请通过以下代码使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数和 [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL 语句来将新产品插入到指定数据库中的 SalesLT.Product 表中。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>更新数据
请通过以下代码使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数和 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL 语句来更新 Azure SQL 数据库中的数据。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>删除数据
请通过以下代码使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数和 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 语句来删除 Azure SQL 数据库中的数据。 将 server、database、username 和 password 参数替换为使用 AdventureWorksLT 示例数据创建数据库时指定的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>后续步骤

- 有关 [Microsoft Python Driver for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) 的详细信息。
- 访问 [Python 开发人员中心](/develop/python/)。
- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio 进行连接和查询，请参阅[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 进行连接和查询，请参阅[使用 PHP 进行连接和查询](sql-database-connect-query-php.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。

