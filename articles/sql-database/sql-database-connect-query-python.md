---
title: "使用 Python 连接 Azure SQL 数据库 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure SQL 数据库的 Python 代码示例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 03/27/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 91e1dcd5b4a7dc62a09c9deb26622dacba1dcaa1
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Python 进行连接和数据查询

使用 [Python](https://python.org) 连接 Azure SQL 数据库并进行查询。 本指南详述了如何使用 Python 连接到 Azure SQL 数据库，然后执行查询、插入、更新和删除语句。

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>配置开发环境
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
安装 [Microsoft ODBC 驱动程序 13.1](https://www.microsoft.com/download/details.aspx?id=53339)。 pyodbc 使用 Linux 上的 Microsoft ODBC 驱动程序连接到 SQL 数据库。 

然后使用 pip 安装 pyodbc

```cmd
pip install pyodbc==3.1.1
```

可在[此处](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到有关使用 pip 的说明

## <a name="get-connection-information"></a>获取连接信息

在 Azure 门户中获取连接字符串。 请使用连接字符串连接到 Azure SQL 数据库。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概要”窗格中，查看完全限定的服务器名称。 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
   
## <a name="select-data"></a>选择数据
使用 [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) 函数和 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 语句查询 Azure SQL 数据库中的数据。 [Cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数可用于针对 SQL 数据库从查询中检索结果集。 此函数实际上可接受任何查询，并返回可使用 [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html) 循环访问的结果集。

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
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
在 SQL 数据库中，可以使用 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 属性和 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 对象自动生成[主键值](https://msdn.microsoft.com/library/ms179610.aspx)。 

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>更新数据
可使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数执行 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 语句，更新 Azure SQL 数据库中的数据。

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```


## <a name="delete-data"></a>删除数据
可使用 [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) 函数执行 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句，删除 Azure SQL 数据库中的数据。

```Python
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>后续步骤
* 请参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)。
* 有关 [Microsoft Python Driver for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) 的详细信息。
* 访问 [Python 开发人员中心](/develop/python/)。
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。

