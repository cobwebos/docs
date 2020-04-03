---
title: 使用动态 SQL
description: 在 Synapse SQL 池中使用动态 SQL 的开发解决方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619038"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Synapse SQL 池中的动态 SQL
本文包括使用 SQL 池中动态 SQL 的开发解决方案的提示。

## <a name="dynamic-sql-example"></a>动态 SQL 示例

在为 SQL 池开发应用程序代码时，您可能需要使用动态 SQL 来帮助提供灵活的通用和模块化解决方案。 SQL 池目前不支持 blob 数据类型。 

不支持 blob 数据类型可能会限制字符串的大小，因为 blob 数据类型包括 varchar(max) 和 nvarchar(max) 类型。 

如果在应用程序代码中使用了这些类型的生成大字符串，则需要将代码分解为块，改用 EXEC 语句。

一个简单的示例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字符串较短，则可以像平时一样使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)。

> [!NOTE]
> 作为动态 SQL 执行的语句仍将受所有 T-SQL 验证规则的约束。
> 
> 

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

