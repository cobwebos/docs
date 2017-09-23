---
title: "SQL 数据仓库中的动态 SQL | Microsoft 文档"
description: "有关在开发解决方案时使用 Azure SQL 数据仓库中的动态 SQL 的技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.contentlocale: zh-cn
ms.lasthandoff: 04/03/2017

---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL 数据仓库中的动态 SQL
在为 SQL 数据仓库开发应用程序代码时，你可能需要使用动态 SQL 来帮助提供灵活、泛型、模块化的解决方案。 SQL 数据仓库目前不支持 Blob 数据类型。 这可能会限制字符串的大小，因为 Blob 类型包括 varchar(max) 和 nvarchar(max) 类型。 如果你在构建极大型字符串时在应用程序代码中使用了这些类型，则需要将代码分解成块，并改用 EXEC 语句。

一个简单的示例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字符串较短，则可以像平时一样使用 [sp_executesql][sp_executesql]。

> [!NOTE]
> 作为动态 SQL 执行的语句仍将遵循所有 TSQL 验证规则。
> 
> 

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述][development overview]。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->

