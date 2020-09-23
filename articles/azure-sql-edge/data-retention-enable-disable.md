---
title: 启用和禁用数据保留策略-Azure SQL Edge
description: 了解如何启用和禁用 Azure SQL Edge 中的数据保留策略
keywords: SQL 边缘，数据保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: ee2d65d66caef5cd9405d6e3d0e094de2e30ae87
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902494"
---
# <a name="enable-and-disable-data-retention-policies"></a>启用和禁用数据保留策略

本主题介绍如何启用和禁用数据库和表的数据保留策略。 

## <a name="enable-data-retention-for-a-database"></a>启用数据库的数据保留

下面的示例演示如何使用 [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)启用数据保留。

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>检查是否为数据库启用了数据保留

以下命令可用于检查是否为数据库启用了数据保留
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>启用表的数据保留

对于您希望数据自动清除的每个表，必须启用数据保留。 当对数据库和表启用了数据保留时，后台系统任务会定期扫描表，以确定并删除任何过时的 (过期) 行。 在表创建过程中，可以使用 [Create table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 或 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)来启用对表的数据保留。

下面的示例演示如何使用 [Create table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql)为表启用数据保留。 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

`WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )`Create table 命令的一部分设置表中的数据保留。 该命令使用以下必需的参数 

- DATA_DELETION-指示数据保留是打开还是关闭。
- 表中列的 FILTER_COLUMN 名称，将用于确定行是否已过时。 筛选列只能是具有以下数据类型的列 
    - Date
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD-一个整数值，后跟一个单元说明符。 允许的单位为 "天"、"天"、"周"、"月"、"月" 和 "年"。

下面的示例演示如何使用 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)对表启用数据保留。  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>检查是否为表启用了数据保留

以下命令可用于检查启用了数据保留功能的表

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

如果值为 data_retention_period =-1 且 data_retention_period_unit 为无限，则表示未对表设置数据保留。

以下查询可用于标识用作数据保留 filter_column 的列。 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>Corelating DB 和表数据保持设置

数据库和表中的数据保留设置结合使用，以确定是否在表中运行自动清除。 

|数据库选项 | 表选项 | 行为 |
|----------------|--------------|----------|
| OFF | OFF | 禁用了数据保留策略，并禁用了对过时记录的自动和手动清除。|
| OFF | ON  | 为表启用了数据保留策略。 已禁用过时记录的自动清除，但手动清理方法可用于清除过时的记录。 |
| ON | OFF | 在数据库级别启用了数据保留策略。 但是，由于在表级别禁用了选项，因此不会清除过时行。|
| ON | ON | 同时为数据库和表启用了数据保留策略。 已启用过时记录的自动清理。 |

## <a name="disable-data-retention-on-a-table"></a>禁用表的数据保留 

可以通过使用 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)对表禁用数据保留。 以下命令可用于禁用表的数据保留。

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>禁用数据库上的数据保留

可以通过使用 [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)来禁用表的数据保留。 以下命令可用于禁用数据库上的数据保留。

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>后续步骤
- [数据保留和自动数据清除](data-retention-overview.md)
- [用保留策略管理历史数据](data-retention-cleanup.md)
