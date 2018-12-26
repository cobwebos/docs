---
title: 使用 Python 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 Python 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271821"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>快速入门：使用 Python 查询 Azure SQL 数据库

 本快速入门演示了如何使用 [Python](https://python.org) 连接到 Azure SQL 数据库，然后使用 Transact-SQL 语句查询数据。 如需进一步的 SDK 详细信息，请查看[参考](https://docs.microsoft.com/python/api/overview/azure/sql)文档、[pyodbc GitHub 存储库](https://github.com/mkleehammer/pyodbc/wiki/)以及 [pyodbc 示例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保符合以下条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 针对用于本快速入门的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal-firewall.md)。
  
- 适用于你的操作系统的 Python 和相关软件：
  
  - **MacOS**：安装 Homebrew 和 Python，接着安装 ODBC 驱动程序和 SQLCMD，再安装 Python Driver for SQL Server。 请参阅 [Create Python apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)（在 macOS 上使用 SQL Server 创建 Python 应用）中的步骤 1.2、1.3 和 2.1。 有关详细信息，请参阅[在 Linux 和 macOS 上安装 Microsoft ODBC 驱动程序](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。
    
  - **Ubuntu**：使用 `sudo apt-get install python python-pip gcc g++ build-essential` 安装 Python 和其他所需的程序包。 下载并安装用于 SQL Server 的 ODBC 驱动程序、SQLCMD 和 Python 驱动程序。 有关说明，请参阅[配置用于 pyodbc Python 开发的开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。
    
  - Windows：安装用于 SQL Server 的 Python、ODBC 驱动程序和 SQLCMD 以及 Python 驱动程序。 有关说明，请参阅[配置用于 pyodbc Python 开发的开发环境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>创建用于查询 SQL 数据库的代码 

1. 在文本编辑器中，创建新文件 sqltest.py。  
   
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

