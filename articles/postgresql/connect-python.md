---
title: "从 Python 连接到 Azure Database for PostgreSQL | Microsoft Docs"
description: "提供了一个 Python 代码示例，你可以使用它来连接到 Azure Database for PostgreSQL 并查询其中的数据。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0027d25bcaa3376c5a29299f3ec88809ebf1d2d8
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---
# Azure Database for PostgreSQL：使用 Python 进行连接并查询数据
<a id="azure-database-for-postgresql-use-python-to-connect-and-query-data" class="xliff"></a>
本快速入门演示了如何通过 Mac OS、Ubuntu Linux 和 Windows 平台使用 [Python](https://python.org) 连接到 Azure Database for PostgreSQL，然后使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本文中的步骤假定你熟悉如何使用 Python 进行开发，但不熟悉如何使用 Azure Database for PostgreSQL。

## 先决条件
<a id="prerequisites" class="xliff"></a>
此快速入门使用以下任意指南中创建的资源作为起点：
- [创建 DB - 门户](quickstart-create-server-database-portal.md)
- [创建 DB - CLI](quickstart-create-server-database-azure-cli.md)

你还需要：
- 安装 [python](https://www.python.org/downloads/)
- 安装 [pip](https://pip.pypa.io/en/stable/installing/) 包（如果使用的是从 [python.org](https://python.org) 下载的 Python 2 >=2.7.9 或 Python 3 >=3.4 二进制文件，则 pip 已安装，但需将其升级。）

## 安装适用于 PostgreSQL 的 Python 连接库
<a id="install-the-python-connection-libraries-for-postgresql" class="xliff"></a>
安装 [psycopg2](http://initd.org/psycopg/docs/install.html) 包，以便连接和查询数据库。 对于最常用的平台（Linux、OSX、Windows）来说，psycopg2 [在 PyPI 上提供](https://pypi.python.org/pypi/psycopg2/)，其形式为 [wheel](http://pythonwheels.com/) 包，因此可以使用 pip install 获取模块的二进制版本，包括所有依赖项：

```cmd
pip install psycopg2
```
确保使用最新版的 pip（可以使用 `pip install -U pip` 之类的命令进行升级）

## 获取连接信息
<a id="get-connection-information" class="xliff"></a>
获取连接到 Azure Database for PostgreSQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户中的左侧菜单中，单击“所有资源”，然后搜索刚创建的服务器 **mypgserver-20170401**。
3. 单击服务器名称 **mypgserver-20170401**。
4. 选择服务器的“概述”页面。 记下“服务器名称”和“服务器管理员登录名”。
 ![Azure Database for PostgreSQL - 服务器管理员登录名](./media/connect-python/1-connection-string.png)
5. 如果忘了服务器的登录信息，请导航到“概览”页，以查看服务器管理员登录名并重置密码（如果需要）。
   
## 进行连接，创建表，然后插入数据
<a id="connect-create-table-and-insert-data" class="xliff"></a>
通过以下代码进行连接，然后使用 [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 函数和 **INSERT** SQL 语句加载数据。 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函数用来针对 PostgreSQL 数据库执行 SQL 查询。 将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

conn.commit()
```

## 读取数据
<a id="read-data" class="xliff"></a>
使用以下代码读取通过 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函数和 **SELECT** SQL 语句插入的数据。 此函数可接受任何查询，并返回可使用 [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) 循环访问的结果集。 将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

conn.commit()
```

## 更新数据
<a id="update-data" class="xliff"></a>
使用以下代码来更新清单行，该行是以前使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函数和 **UPDATE** SQL 语句插入的。 将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

conn.commit()
```

## 删除数据
<a id="delete-data" class="xliff"></a>
使用以下代码来删除清单项，该项是以前使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函数和 **DELETE** SQL 语句插入的。 将 host、dbname、user 和 password 参数替换为创建服务器和数据库时指定的值。

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print ("Deleted 1 row of data")

conn.commit()
```

## 后续步骤
<a id="next-steps" class="xliff"></a>
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./howto-migrate-using-export-and-import.md)

