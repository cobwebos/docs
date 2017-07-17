---
title: "使用 Python 查询 Azure SQL 数据库 | Microsoft Docs"
description: "本主题介绍如何使用 Python 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 07/11/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: b7c217be41b979f8a7246109cc95a01341dadf3d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# 使用 Python 查询 Azure SQL 数据库
<a id="use-python-to-query-an-azure-sql-database" class="xliff"></a>

 本快速入门演示了如何使用 [Python](https://python.org) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句查询数据。

## 先决条件
<a id="prerequisites" class="xliff"></a>

若要完成本快速入门教程，请确保符合以下条件：

- Azure SQL 数据库。 此快速入门使用以下某个快速入门中创建的资源： 

   - [创建 DB - 门户](sql-database-get-started-portal.md)
   - [创建 DB - CLI](sql-database-get-started-cli.md)
   - [创建 DB - PowerShell](sql-database-get-started-powershell.md)

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 已为操作系统安装 Python 和相关软件。

    - **MacOS**：安装 Homebrew 和 Python，接着安装 ODBC 驱动程序和 SQLCMD，再安装 Python Driver for SQL Server。 请参阅[步骤 1.2、1.3 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/Python/mac/)。
    - **Ubuntu**：安装 Python 和其他所需包，然后安装 Python Driver for SQL Server。 请参阅[步骤 1.2 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)。
    - **Windows**：安装最新版的 Python（现已为你配置环境变量），安装 ODBC 驱动程序和 SQLCMD，然后安装 Python Driver for SQL Server。 请参阅[步骤 1.2、1.3 和 2.1](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)。 

## SQL Server 连接信息
<a id="sql-server-connection-information" class="xliff"></a>

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概览”页上，查看如下图所示的完全限定的服务器名称。 可以将鼠标悬停在服务器名称上以打开“单击以复制”选项。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. 如果忘了服务器的登录信息，请导航到 SQL 数据库服务器页，以查看服务器管理员名称并重置密码（如果需要）。     
    
## 插入用于查询 SQL 数据库的代码
<a id="insert-code-to-query-sql-database" class="xliff"></a> 

1. 在喜欢的文本编辑器中，创建新文件 sqltest.py。  

2. 将内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

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
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## 运行代码
<a id="run-the-code" class="xliff"></a>

1. 请在命令提示符处运行以下命令：

   ```Python
   python sqltest.py
   ```

2. 验证是否已返回前 20 行，然后关闭应用程序窗口。

## 后续步骤
<a id="next-steps" class="xliff"></a>

- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [用于 SQL Server 的 Microsoft Python 驱动程序](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 开发人员中心](https://azure.microsoft.com/develop/python/?v=17.23h)


