---
title: "使用 Python 连接 SQL 数据库| Microsoft 文档"
description: "演示了一个可以用来连接到 Azure SQL 数据库的 Python 代码示例。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: app development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/05/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5f3a4e49646063b41af5a9941f27291762f5336e


---
# <a name="connect-to-sql-database-by-using-python"></a>使用 Python 连接到 SQL 数据库
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主题说明如何使用 Python 来连接和查询 Azure SQL 数据库。 可以从 Windows、Ubuntu Linux 或 Mac 平台运行此示例。

## <a name="step-1-create-a-sql-database"></a>步骤 1：创建 SQL 数据库
请参阅[入门页](sql-database-get-started.md)，以了解如何创建示例数据库。  必须根据指南创建 **AdventureWorks** 数据库模板。 下面所示的示例只适用于 **AdventureWorks 架构**。 创建数据库后，请确保根据[入门页](sql-database-get-started.md)中所述，通过启用防火墙规则来启用对 IP 地址的访问

## <a name="step-2-configure-development-environment"></a>步骤 2：配置开发环境
### <a name="mac-os"></a>**Mac OS**
### <a name="install-the-required-modules"></a>安装所需的模块
打开终端并安装

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql==2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
打开终端并导航到你要在其中创建 python 脚本的目录。 输入以下命令以安装 **FreeTDS** 和 **pymssql**。 pymssql 使用 FreeTDS 连接到 SQL 数据库。

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql==2.1.1

### <a name="windows"></a>**Windows**
从[**此处**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)安装 pymssql。 

确保选择正确的 whl 文件。 例如：如果在 64 位计算机上使用 Python 2.7，请选择：pymssql‑2.1.1‑cp27‑none‑win_amd64.whl。 下载 .whl 文件后，请将它放入 C:/Python27 文件夹。

现在使用 pip 从命令行安装 pymssql 驱动程序。 通过 cd 进入 C: / Python27 并运行以下项

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

可在[此处](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)找到有关使用 pip 的说明

## <a name="step-3-run-sample-code"></a>步骤 3：运行示例代码
创建名为 **sql_sample.py** 的文件并在其中粘贴以下代码。 可以通过命令行使用以下命令运行此操作：

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>连接到 SQL 数据库
 [Pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 函数用于连接到 SQL 数据库。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>执行 SQL SELECT 语句
 [Cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 函数可用于针对 SQL 数据库从查询中检索结果集。 此函数实际上可接受任何查询，并返回可使用 [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone) 循环访问的结果集。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>插入一行，传递参数，然后检索生成的主键
在 SQL 数据库中，可以使用 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 属性和 [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) 对象自动生成[主键值](https://msdn.microsoft.com/library/ms179610.aspx)。 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>事务
此代码示例演示了你可以在其中执行以下操作的事务的用法：

* 开始一个事务
* 插入一行数据
* 回滚事务以撤消插入 

在 sql_sample.py 中粘贴以下代码。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>后续步骤
* 参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)
* 有关 [Microsoft Python Driver for SQL Server](https://msdn.microsoft.com/library/mt652092.aspx) 的详细信息
* 访问 [Python 开发人员中心](/develop/python/)。

## <a name="additional-resources"></a>其他资源
* [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。




<!--HONumber=Nov16_HO3-->


