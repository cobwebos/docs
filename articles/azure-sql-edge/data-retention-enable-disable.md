---
title: 启用和禁用数据保留策略 - Azure SQL Edge
description: 了解如何启用和禁用 Azure SQL Edge 中的数据保留策略
keywords: SQL Edge, 数据保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: ee2d65d66caef5cd9405d6e3d0e094de2e30ae87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902494"
---
# <a name="enable-and-disable-data-retention-policies"></a>启用和禁用数据保留策略

本主题说明如何为数据库和表启用和禁用数据保留策略。 

## <a name="enable-data-retention-for-a-database"></a>为数据库启用数据保留

下面的示例演示如何使用 [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) 来启用数据保留。

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>检查是否已为数据库启用了数据保留

以下命令可用于检查是否已为数据库启用了数据保留
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>为表启用数据保留

必须为要自动清理其数据的每个表启用数据保留。 如果为数据库和表启用了数据保留，后台系统任务会定期扫描表，以识别并删除任何已过时的行。 可以在表创建过程中使用 [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 或使用 [Alter Table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) 来对表启用数据保留。

下面的示例演示如何使用 [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 来为表启用数据保留。 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

create table 命令的 `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` 部分设置表的数据保留。 该命令使用以下必需的参数 

- DATA_DELETION - 指示数据保留是 ON 还是 OFF。
- FILTER_COLUMN - 表中列的名称，用于确定行是否已过时。 筛选列只能是具有以下数据类型的列 
    - Date
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD - 一个整数值，后跟一个单位描述符。 允许的单位为 DAY、DAYS、WEEK、WEEKS、MONTH、MONTHS、YEAR 和 YEARS。

下面的示例演示如何使用 [Alter Table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) 来为表启用数据保留。  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>检查是否已为表启用了数据保留

以下命令可用于检查已启用数据保留的表

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

如果 data_retention_period 值 = -1 且 data_retention_period_unit 为 INFINITE，则表示未对表设置数据保留。

以下查询可用于标识用作数据保留的 filter_column 的列。 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>关联 DB 和表数据保留设置

数据库和表中的数据保留设置结合使用，以确定是否在表上运行对过期行的自动清理。 

|数据库选项 | 表选项 | 行为 |
|----------------|--------------|----------|
| OFF | OFF | 已禁用数据保留策略，且已禁用对过时记录的自动和手动清理。|
| OFF | ON  | 已为表启用数据保留策略。 已禁用过时记录的自动清理，但手动清理方法可用于清理过时的记录。 |
| ON | OFF | 已在数据库级别启用数据保留策略。 但是，由于在表级别禁用了该选项，因此不存在基于保留的过期行清理。|
| ON | ON | 已同时为数据库和表启用数据保留策略。 已启用过时记录的自动清理。 |

## <a name="disable-data-retention-on-a-table"></a>禁用表上的数据保留 

可以使用 [Alter Table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) 来禁用表上的数据保留。 以下命令可用于禁用表上的数据保留。

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>禁用数据库上的数据保留

可以使用 [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) 来禁用表上的数据保留。 以下命令可用于禁用数据库上的数据保留。

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>后续步骤
- [数据保留和自动数据清理](data-retention-overview.md)
- [使用保留策略管理历史数据](data-retention-cleanup.md)
