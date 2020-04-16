---
title: 在突触 SQL 中使用动态 SQL
description: 在突触 SQL 中使用动态 SQL 的提示。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429598"
---
# <a name="dynamic-sql-in-synapse-sql"></a>同步 SQL 中的动态 SQL
在本文中，您将找到使用动态 SQL 和使用 Synapse SQL 开发解决方案的提示。

## <a name="dynamic-sql-example"></a>动态 SQL 示例

在开发应用程序代码时，您可能需要使用动态 SQL 来帮助提供灵活的通用和模块化解决方案。

> [!NOTE]
> SQL 池目前不支持 blob 数据类型。 不支持 blob 数据类型可能会限制字符串的大小，因为 blob 数据类型包括 varchar(max) 和 nvarchar(max) 类型。 如果已在应用程序代码中使用这些类型构建大型字符串，则需要将代码分解成块，并改用 EXEC 语句。

一个简单的示例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字符串较短，则可以像平时一样使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

> [!NOTE]
> 作为动态 SQL 执行的语句仍将受所有 T-SQL 验证规则的约束。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](develop-overview.md)。
