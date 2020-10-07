---
title: 快速入门：使用 Python 进行连接 - Azure Database for PostgreSQL 灵活服务器
description: 本快速入门提供了多个 Python 代码示例，你可以使用它们来连接到 Azure Database for PostgreSQL 灵活服务器并查询其中的数据。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 89dc36a9b1b1fee9ad10d55945c7fc17bf72f476
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944048"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>快速入门：使用 Python 连接到 Azure Database for PostgreSQL 灵活服务器并查询其中的数据

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

在本快速入门中，你将使用 Python 连接到 Azure Database for PostgreSQL 灵活服务器。 然后使用 SQL 语句在 Mac、Ubuntu Linux 和 Windows 平台的数据库中查询、插入、更新和删除数据。 

本文假设你熟悉如何使用 Python 进行开发，但不熟悉 Azure Database for PostgreSQL 灵活服务器的用法。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* Azure Database for PostgreSQL 灵活服务器。 若要创建灵活服务器，请参阅[使用 Azure 门户创建 Azure Database for PostgreSQL 灵活服务器](./quickstart-create-server-portal.md)。
* [Python](https://www.python.org/downloads/) 2.7.9+ 或 3.4+。
* 最新 [pip](https://pip.pypa.io/en/stable/installing/) 包安装程序。

## <a name="preparing-your-client-workstation"></a>准备客户端工作站
- 如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的 VNet 中。 请参阅[使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。 请参阅[使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 灵活服务器防火墙规则](./how-to-manage-firewall-cli.md)。

## <a name="install-the-python-libraries-for-postgresql"></a>安装适用于 PostgreSQL 的 Python 库
[psycopg2](https://pypi.python.org/pypi/psycopg2/) 模块可连接到 PostgreSQL 数据库并对其进行查询，并可作为 Linux、macOS 或 Windows [轮](https://pythonwheels.com/)包提供。 安装二进制版本的模块，包括所有依赖项。 有关 `psycopg2` 安装和要求的详细信息，请参阅[安装](http://initd.org/psycopg/docs/install.html)。 

若要安装 `psycopg2`，请打开终端或命令提示符并运行命令 `pip install psycopg2`。

## <a name="get-database-connection-information"></a>获取数据库连接信息
连接到 Azure Database for PostgreSQL 灵活服务器需要完全限定的服务器名称和登录凭据。 可以从 Azure 门户获取此信息。

1. 在 [Azure 门户](https://portal.azure.com/)中，搜索并选择灵活服务器的名称。 
2. 在服务器的“概述”页上，复制完全限定的“服务器名称”和“管理员用户名”  。 完全限定的“服务器名称”始终为“\<my-server-name>.postgres.database.azure.com”的格式。

   你还需要管理员密码。 如果忘记，可以从概述页重置它。 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>如何运行 Python 示例

对于本文中的每个代码示例：

1. 在文本编辑器中创建新的文件。 

1. 将代码示例添加到文件。 在代码中，进行以下替换：
   - `<server-name>` 和 `<admin-username>` 替换为从 Azure 门户复制的值。
   - `<admin-password>` 替换为服务器密码。
   - `<database-name>` 替换为 Azure Database for PostgreSQL 灵活服务器数据库的名称。 创建服务器时，会自动创建一个名为 postgres 的默认数据库。 你可以重命名该数据库，或使用 SQL 命令创建新的数据库。 

1. 将文件保存在具有 .py 扩展名的项目文件夹（如 postgres-insert.py）中 。 对于 Windows，确保在保存文件时选择 UTF-8 编码。 

1. 若要运行该文件，请在命令行界面中切换到项目文件夹，然后键入 `python` 后跟文件名，例如 `python postgres-insert.py`。

## <a name="create-a-table-and-insert-data"></a>创建表并插入数据
下面的代码示例使用 [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 函数连接到 Azure Database for PostgreSQL 灵活服务器数据库，并使用 SQL INSERT 语句加载数据。 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 函数对数据库执行 SQL 查询。 

```Python
import psycopg2
# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")
# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")
# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")
# Clean up
conn.commit()
cursor.close()
conn.close()
```

如果代码成功运行，则会生成以下输出：

![命令行输出](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>读取数据
下面的代码示例连接到 Azure Database for PostgreSQL 灵活服务器数据库，并使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 和 SQL SELECT 语句来读取数据。 此函数可接受查询，并返回可使用 [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) 循环访问的结果集。 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>更新数据
下面的代码示例连接到 Azure Database for PostgreSQL 灵活服务器数据库，并使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 和 SQL UPDATE 语句来更新数据。 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>删除数据
下面的代码示例连接到 Azure Database for PostgreSQL 灵活服务器数据库，并使用 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 和 SQL DELETE 语句来删除以前插入的库存项。 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用转储和还原迁移数据库](../howto-migrate-using-dump-and-restore.md)