---
title: 连接到 Azure SQL 数据仓库 | Microsoft Docs
description: 连接到 Azure SQL 数据仓库的连接概述
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/27/2016
ms.author: sonyama;barbkess

---
# 连接到 Azure SQL 数据仓库
> [!div class="op_single_selector"]
> * [概述](sql-data-warehouse-connect-overview.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [驱动程序](sql-data-warehouse-connection-strings.md)
> 
> 

连接到 Azure SQL 数据仓库的概述。

## 发现门户中的连接字符串
SQL 数据仓库与 Azure SQL 服务器相关联。若要连接，需要完全限定的服务器名称。例如，**myserver**.database.windows.net。

若要查找完全限定的服务器名称，请执行以下操作：

1. 转到 [Azure 门户][Azure 门户]。
2. 单击“SQL 数据库”，然后单击想要连接的数据库。本示例使用 AdventureWorksDW 示例数据库。
3. 找到完整的服务器名称。
   
    ![完整服务器名称][1]

## 连接设置
SQL 数据仓库在连接和创建对象期间标准化一些设置。无法重写这些设置。

| 数据库设置 | 值 |
|:--- |:--- |
| [ANSI\_NULLS][ANSI\_NULLS] |开 |
| [QUOTED\_IDENTIFIERS][QUOTED\_IDENTIFIERS] |开 |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## 监视连接和查询
建立连接和会话之后，可以编写查询并将其提交到 SQL 数据仓库。若要了解如何监视会话和查询，请参阅[使用 DMV 监视工作负荷][使用 DMV 监视工作负荷]。

## 后续步骤
若要开始使用 Visual Studio 和其他应用程序查询数据仓库，请参阅[使用 Visual Studio 进行查询][使用 Visual Studio 进行查询]。

<!--Articles-->

[使用 Visual Studio 进行查询]: ./sql-data-warehouse-query-visual-studio.md
[使用 DMV 监视工作负荷]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure 门户]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0921_2016-->