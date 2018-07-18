---
title: 定义数据类型 - Azure SQL 数据仓库 | Microsoft Docs
description: 有关在 Azure SQL 数据仓库中定义表数据类型的建议。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523500"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的表数据类型
有关在 Azure SQL 数据仓库中定义表数据类型的建议。 

## <a name="what-are-the-data-types"></a>数据类型有哪些？

SQL 数据仓库支持最常用的数据类型。 有关受支持数据类型的列表，请参阅 CREATE TABLE 语句中的[数据类型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)。 

## <a name="minimize-row-length"></a>最大限度缩短行长度
最大限度减小数据类型大小可缩短行长度，获得更好的查询性能。 使用适用于数据的最小数据类型。 

- 避免使用较大默认长度定义字符列。 例如，如果最长的值是 25 个字符，则将列定义为 VARCHAR(25)。 
- 当仅需要 VARCHAR 时请避免使用 [NVARCHAR][NVARCHAR]
- 尽可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。

如果使用 PolyBase 外部表来加载表，则定义的表行长度不能超过 1 MB。 当数据长度可变的行超过 1 MB 时，可使用 BCP 而不是 PolyBase 加载行。

## <a name="identify-unsupported-data-types"></a>识别不支持的数据类型
如果从另一个 SQL 数据库迁移数据库，可能会遇到 SQL 数据仓库不支持的数据类型。 使用此查询发现现有 SQL 架构中不支持的数据类型。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>对不受支持的数据类型的解决方法

以下列表显示 SQL 数据仓库不支持的数据类型，同时提供可替代不支持的数据类型的可用数据类型。

| 不支持的数据类型 | 解决方法 |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [图片](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |将列拆分成多个强类型化列。 |
| [table](/sql/t-sql/data-types/table-transact-sql) |转换成暂时表。 |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |重写代码来使用 [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) 和 [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) 函数。 仅支持常量作为默认值，因此，不能将 current_timestamp 定义为默认约束。 如果需要从 timestamp 类型化列迁移行版本值，请为 NOT NULL 或 NULL 行版本值使用 [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) 或 [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8)。 |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [用户定义的类型](/sql/relational-databases/native-client/features/using-user-defined-types) |尽可能转换回本机数据类型。 |
| 默认值 | 默认值仅支持文本和常量。 |


## <a name="next-steps"></a>后续步骤
有关开发表的详细信息，请参阅[表概述](sql-data-warehouse-tables-overview.md)。
