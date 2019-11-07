---
title: 连接 sqlcmd
description: 使用 sqlcmd 命令行实用程序连接并查询 Azure SQL 数据仓库。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685737"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>使用 sqlcmd 连接到 SQL 数据仓库
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用[sqlcmd][sqlcmd]命令行实用程序连接到 Azure SQL 数据仓库并进行查询。  

## <a name="1-connect"></a>1. 连接
若要开始使用 [sqlcmd][sqlcmd]，请打开命令提示符并输入 **sqlcmd** ，后跟 SQL 数据仓库数据库的连接字符串。 连接字符串需要以下参数：

* **服务器 (-S)：** 采用 `<`Server Name`>`.database.windows.net 格式的服务器
* **数据库 (-d)：** 数据库名称。
* **启用带引号的标识符 (-I)：** 必须启用带引号的标识符才能连接到 SQL 数据仓库实例。

若要使用 SQL Server 身份验证，需要添加用户名/密码参数：

* **用户 (-U)：** 采用`<`用户`>`格式的服务器用户
* **密码 (-P)：** 与用户关联的密码

例如，连接字符串可能如下所示：

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

若要使用 Azure Active Directory 集成身份验证，需添加 Azure Active Directory 参数：

* **Azure Active Directory 身份验证 (-G)：** 使用 Azure Active Directory 进行身份验证

例如，连接字符串可能如下所示：

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> 需 [启用 Azure Active Directory 身份验证](sql-data-warehouse-authentication.md) 才能使用 Active Directory 进行身份验证。
> 
> 

## <a name="2-query"></a>2. 查询
连接后，可以对实例发出任何支持的 Transact-SQL 语句。  在此示例中，查询以交互模式进行提交。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

后续示例演示如何通过使用 -Q 选项或将 SQL 输送到 sqlcmd 从而在批处理模式下运行查询。

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>后续步骤
有关 sqlcmd 中可用选项的详细信息，请参阅[sqlcmd 文档][sqlcmd]。

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
