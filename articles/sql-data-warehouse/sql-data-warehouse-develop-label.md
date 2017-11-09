---
title: "在 SQL 数据仓库中使用标签检测查询 | Microsoft 文档"
description: "有关在开发解决方案时于 Azure SQL 数据仓库中使用标签检测查询的技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>在 SQL 数据仓库中使用标签检测查询
SQL 数据仓库支持称为查询标签的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串 'My Query Label' 标记为查询。 此字符串特别有用，因为可以通过 DMV 查询标签。 这为我们提供了一种机制用于跟踪问题查询，以及帮助通过 ETL 运行来了解进度。

良好的命名约定确实很有帮助。 例如，类似 ' PROJECT : PROCEDURE : STATEMENT : COMMENT' 的内容有助于在源代码管理的几乎所有代码中唯一标识查询。

若要按标签进行搜索，可以运行以下使用动态管理视图的查询：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查询时，必须以方括号或双引号括住文字标签。 标签是一个保留字，如果未分隔，会导致错误。
> 
> 

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述][development overview]。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
