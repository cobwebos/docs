---
title: 使用 Python 查询数据库
description: 本主题介绍如何使用 Python 创建连接到 Azure SQL 数据库中的数据库的程序并使用 Transact-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 5257e8730830c6b51808d5d014bc3bdec6036bfe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843957"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：使用 Python 查询 Azure SQL 数据库中的数据库或 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在本快速入门中，你将使用 Python 连接到 Azure SQL 数据库或 Azure SQL 托管实例，并使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

  | 操作 | SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 创建| [门户](single-database-create-quickstart.md) | [门户](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 配置 | [服务器级别 IP 防火墙规则](firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)|
  |||[来自本地的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](database-import.md) 文件还原或导入 Adventure Works| 从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](database-import.md) 文件还原或导入 Adventure Works|
  |||

- [Python](https://python.org/downloads) 3 和相关软件

  # <a name="macos"></a>[macOS](#tab/macos)

  若要安装 Homebrew 和 Python、ODBC 驱动程序和 SQLCMD 以及适用于 SQL Server 的 Python 驱动程序，请使用[在 macOS 上创建使用 SQL Server 的 Python 应用](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的步骤 **1.2**、**1.3** 和 **2.1**。

  有关更多信息，请参阅 [macOS 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  若要安装 Python 和其他所需的包，请使用 `sudo apt-get install python python-pip gcc g++ build-essential`。

  若要安装 ODBC 驱动程序、SQLCMD 以及适用于 SQL Server 的 Python 驱动程序，请参阅[配置 pyodbc Python 开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。

  有关更多信息，请参阅 [Linux 上的 Microsoft ODBC 驱动程序](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="windows"></a>[Windows](#tab/windows)

  若要安装 Python、ODBC 驱动程序、SQLCMD 以及适用于 SQL Server 的 Python 驱动程序，请参阅[配置 pyodbc Python 开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

  有关更多信息，请参阅 [Microsoft ODBC 驱动程序](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)。

---

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 可以选择使用 Azure SQL 托管实例。
>
> 若要执行创建和配置操作，请使用 [Azure 门户](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然后设置[本地](../managed-instance/point-to-site-p2s-configure.md)或 [VM](../managed-instance/connect-vm-instance-configure.md) 连接性。
>
> 若要加载数据，请参阅[通过 BACPAC 进行还原](database-import.md)（使用 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 文件），或参阅[还原 Wide World Importers 数据库](../managed-instance/restore-sample-database-quickstart.md)。

若要进一步了解 Python 和 Azure SQL 数据库中的数据库，请参阅[适用于 Python 的 Azure SQL 数据库库](/python/api/overview/azure/sql)、[pyodbc 存储库](https://github.com/mkleehammer/pyodbc/wiki/)和 [pyodbc 示例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="get-server-connection-information"></a>获取服务器连接信息

获取连接到 Azure SQL 数据库中的数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 转到“SQL 数据库”或“SQL 托管实例”页 。

3. 在“概述”页上，在“Server 名称”旁查看 Azure SQL 数据库中的数据库的完全限定服务器名称，或在“Host”旁边查看 Azure VM 上 Azure SQL 托管实例中或 SQL Server 的完全限定服务器名称（或 IP 地址）  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!NOTE]
> 有关 Azure VM 上的 SQL Server 的连接信息，请参阅[连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="create-code-to-query-your-database"></a>创建用于查询数据库的代码 

1. 在文本编辑器中，创建新文件 sqltest.py。  
   
1. 添加以下代码。 将 \<server>、\<database>、\<username> 和 \<password> 的值替换为自己的值。
   
   >[!IMPORTANT]
   >本示例中的代码使用示例 AdventureWorksLT 数据，在创建数据库时可以选择该数据作为源。 如果数据库有不同数据，请在 SELECT 查询中使用自己数据库中的表。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
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

1. 确认已返回“类别/产品”的前 20 行，然后关闭命令窗口。

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL 数据库中设计第一个数据库](design-first-database-tutorial.md)
- [用于 SQL Server 的 Microsoft Python 驱动程序](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 开发人员中心](https://azure.microsoft.com/develop/python/?v=17.23h)

