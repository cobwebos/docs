---
title: 将数据从 CSV 文件加载到数据库（bcp）
description: 对于较小的数据，请使用 bcp 将数据导入到 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: a5c09ef5ca1e3a4f47c4fd9d7d022c000378a6d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84032998"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>从 CSV 将数据加载到 Azure SQL 数据库或 SQL 托管实例（平面文件）
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

可以使用 bcp 命令行实用程序将数据从 CSV 文件导入 Azure SQL 数据库或 Azure SQL 托管实例。

## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，你需要：

* Azure SQL 数据库中的数据库
* 已安装 bcp 命令行实用工具
* 已安装 sqlcmd 命令行实用工具

可以从 [Microsoft 下载中心][Microsoft Download Center]下载 bcp 和 sqlcmd 实用工具。

### <a name="data-in-ascii-or-utf-16-format"></a>采用 ASCII 或 UTF-16 格式的数据

如果使用自己的数据尝试学习本教程，则数据需要使用 ASCII 或 UTF-16 编码，因为 bcp 不支持 UTF-8。

## <a name="1-create-a-destination-table"></a>1. 创建目标表

将 SQL 数据库中的某个表定义为目标表。 该表中的列必须对应于数据文件每一行中的数据。

若要创建表，请打开命令提示符并使用 sqlcmd.exe 运行以下命令：

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. 创建源数据文件

打开记事本，将以下几行数据复制到新文本文件，然后将此文件保存到本地临时目录，路径为 C:\Temp\DimDate2.txt。 此数据采用 ASCII 格式。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

（可选）若要从 SQL Server 数据库导出自己的数据，请打开命令提示符并运行以下命令。 将 TableName、ServerName、DatabaseName、Username 和 Password 替换成自己的信息。

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. 加载数据

要加载数据，请打开命令提示符并运行以下命令，请注意将 Server Name、Database Name、Username 和 Password 替换成自己的信息。

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

使用此命令来验证是否已正确加载数据

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

结果应如下所示：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>后续步骤

若要迁移 SQL Server 数据库，请参阅 [SQL Server 数据库迁移](database/migrate-to-database-from-sql-server.md)。

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
