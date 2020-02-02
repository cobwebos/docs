---
title: 使用 Python 查询数据库
description: 本主题介绍如何使用 Python 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768575"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>快速入门：使用 Python 查询 Azure SQL 数据库

在本快速入门中，你将使用 Python 连接到 Azure SQL 数据库，并使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个 [Azure SQL 数据库](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 和相关软件

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  若要安装 Homebrew 和 Python、ODBC 驱动程序和 SQLCMD 以及适用于 SQL Server 的 Python 驱动程序，请使用[在 macOS 上创建使用 SQL Server 的 Python 应用](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的步骤 **1.2**、**1.3** 和 **2.1**。

  有关更多信息，请参阅 [macOS 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  若要安装 Python 和其他所需的包，请使用 `sudo apt-get install python python-pip gcc g++ build-essential`。

  若要安装 ODBC 驱动程序、SQLCMD 以及适用于 SQL Server 的 Python 驱动程序，请参阅[配置 pyodbc Python 开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。

  有关更多信息，请参阅 [Linux 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  若要安装 Python、ODBC 驱动程序、SQLCMD 以及适用于 SQL Server 的 Python 驱动程序，请参阅[配置 pyodbc Python 开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

  有关更多信息，请参阅 [Microsoft ODBC 驱动程序](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)。

---

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 可以选择使用 Azure SQL 托管实例。
>
> 若要执行创建和配置操作，请使用 [Azure 门户](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然后设置[现场](sql-database-managed-instance-configure-p2s.md)或 [VM](sql-database-managed-instance-configure-vm.md) 连接性。
>
> 若要加载数据，请参阅[通过 BACPAC 进行还原](sql-database-import.md)（使用 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 文件），或参阅[还原 Wide World Importers 数据库](sql-database-managed-instance-get-started-restore.md)。

若要进一步了解 Python 和 Azure SQL 数据库，请参阅[适用于 Python 的 Azure SQL 数据库库](/python/api/overview/azure/sql)、[pyodbc 存储库](https://github.com/mkleehammer/pyodbc/wiki/)和 [pyodbc 示例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 转到“SQL 数据库”或“SQL 托管实例”页。  

3. 在“概览”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称，或者托管实例的“主机”旁边的完全限定的服务器名称    。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标  。

## <a name="create-code-to-query-your-sql-database"></a>创建用于查询 SQL 数据库的代码 

1. 在文本编辑器中，创建新文件 sqltest.py  。  
   
1. 添加以下代码。 将自己的值替换为 \<服务器>、\<数据库>、\<用户名> 和 \<密码>。
   
   >[!IMPORTANT]
   >本示例中的代码使用示例 AdventureWorksLT 数据，在创建数据库时可以选择该数据作为源。 如果数据库有不同数据，请在 SELECT 查询中使用自己数据库中的表。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>运行代码

1. 请在命令提示符处运行以下命令：

   ```cmd
   python sqltest.py
   ```

1. 确认已返回前 20 个类别/产品行，然后关闭命令窗口。

## <a name="next-steps"></a>后续步骤

- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [用于 SQL Server 的 Microsoft Python 驱动程序](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 开发人员中心](https://azure.microsoft.com/develop/python/?v=17.23h)

