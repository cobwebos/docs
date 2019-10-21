---
title: 快速入门：使用 Python 查询 Azure SQL 数据库
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
ms.openlocfilehash: 32861fa8f8756c4e8d30c055582789d41a92fe7c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331860"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>快速入门：使用 Python 查询 Azure SQL 数据库

 本文演示了如何使用 [Python](https://python.org) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句查询数据。 如需进一步的 SDK 详细信息，请查看[参考](https://docs.microsoft.com/python/api/overview/azure/sql)文档、[pyodbc GitHub 存储库](https://github.com/mkleehammer/pyodbc/wiki/)以及 [pyodbc 示例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保符合以下条件：

- Azure SQL 数据库。 可以根据下述快速入门之一，在 Azure SQL 数据库中创建数据库，然后对其进行配置：

  || 单一数据库 | 托管实例 |
  |:--- |:--- |:---|
  | 创建| [门户](sql-database-single-database-get-started.md) | [门户](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 配置 | [服务器级别 IP 防火墙规则](sql-database-server-level-firewall-rule.md)| [从 VM 进行连接](sql-database-managed-instance-configure-vm.md)|
  |||[从现场进行连接](sql-database-managed-instance-configure-p2s.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](sql-database-import.md) 文件还原或导入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中脚本的编写目的是使用 Adventure Works 数据库。 使用托管实例时，必须将 Adventure Works 数据库导入一个实例数据库，或者修改本文中的脚本，以便使用 Wide World Importers 数据库。
  
- 适用于你的操作系统的 Python 和相关软件：
  
  - **MacOS**：安装 Homebrew 和 Python，接着安装 ODBC 驱动程序和 SQLCMD，再安装 Python Driver for SQL Server。 请参阅 [Create Python apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)（在 macOS 上使用 SQL Server 创建 Python 应用）中的步骤 1.2、1.3 和 2.1。 有关详细信息，请参阅[在 Linux 和 macOS 上安装 Microsoft ODBC 驱动程序](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  - **Ubuntu**：使用 `sudo apt-get install python python-pip gcc g++ build-essential` 安装 Python 和其他所需的程序包。 下载并安装用于 SQL Server 的 ODBC 驱动程序、SQLCMD 和 Python 驱动程序。 有关说明，请参阅[配置用于 pyodbc Python 开发的开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。

  - Windows  ：安装用于 SQL Server 的 Python、ODBC 驱动程序和 SQLCMD 以及 Python 驱动程序。 有关说明，请参阅[配置用于 pyodbc Python 开发的开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

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

