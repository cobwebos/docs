---
title: 使用动态 SQL
description: 在 Synapse SQL 池中使用动态 SQL 开发解决方案的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bce79b8e18b3ec6f1fd139af280086281bbdda98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213459"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Synapse SQL 池中的动态 SQL

本文提供了在 SQL 池中使用动态 SQL 开发解决方案的技巧。

## <a name="dynamic-sql-example"></a>动态 SQL 示例

针对 SQL 池开发应用程序代码时，可能需要借助动态 SQL 来提供灵活、通用且模块化的解决方案。 SQL 池目前不支持 blob 数据类型。

不支持 blob 数据类型可能会限制字符串的大小，因为 blob 数据类型包括 varchar(max) 和 nvarchar(max) 类型。

如果已在应用程序代码中使用这些类型构建大型字符串，则需将代码分解成区块，并改用 EXEC 语句。

一个简单的示例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字符串较短，则可以像平时一样使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

> [!NOTE]
> 作为动态 SQL 执行的语句仍会受所有 T-SQL 验证规则的约束。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
