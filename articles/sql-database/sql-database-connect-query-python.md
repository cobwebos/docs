---
title: 使用 Python 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 Python 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: python
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: carlrab
ms.openlocfilehash: f22a6d38648cf60bac09a751697e1c1d63d1db4f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="use-python-to-query-an-azure-sql-database"></a>使用 Python 查询 Azure SQL 数据库

 此快速入门教程演示如何使用 [Python](https://python.org) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句查询数据。 如需进一步的 SDK 详细信息，请签出[参考](https://docs.microsoft.com/python/api/overview/azure/sql)文档、pyodbc [示例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)以及 [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) GitHub 存储库。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门教程，请确保符合以下条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 已为操作系统安装 Python 和相关软件：

    - **MacOS**：安装 Homebrew 和 Python，接着安装 ODBC 驱动程序和 SQLCMD，再安装 Python Driver for SQL Server。 请参阅[步骤 1.2、1.3 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)。
    - **Ubuntu**：安装 Python 和其他所需包，然后安装 Python Driver for SQL Server。 请参阅[步骤 1.2、1.3 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/)。
    - **Windows**：安装最新版的 Python（现已为你配置环境变量），安装 ODBC 驱动程序和 SQLCMD，然后安装 Python Driver for SQL Server。 请参阅[步骤 1.2、1.3 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/windows/)。 

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>插入用于查询 SQL 数据库的代码 

1. 在喜欢的文本编辑器中，创建新文件 sqltest.py。  

2. 将内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>运行代码

1. 请在命令提示符处运行以下命令：

   ```Python
   python sqltest.py
   ```

2. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## <a name="next-steps"></a>后续步骤

- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [用于 SQL Server 的 Microsoft Python 驱动程序](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 开发人员中心](https://azure.microsoft.com/develop/python/?v=17.23h)

