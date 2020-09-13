---
title: 在 Synapse SQL 中使用查询标签
description: 本文中包含的是有关在 Synapse SQL 中使用查询标签的基本技巧。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 596b18332fc490f72ad185699fc59bf4521eca69
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032174"
---
# <a name="use-query-labels-in-synapse-sql"></a>在 Synapse SQL 中使用查询标签
本文中包含的是有关在 Synapse SQL 中使用查询标签的基本技巧。

> [!NOTE]
> SQL 点播 (预览) 不支持标签查询。

## <a name="what-are-query-labels"></a>什么是查询标签
SQL 池支持称为“查询标签”的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记特别有用，因为可以通过 DMV 查询标签。 查询标签提供了一种机制，可用于查找问题查询，并有助于通过 ELT 运行来确定进度。

良好的命名约定最为有用。 例如，让标签以 PROJECT、PROCEDURE、STATEMENT 或 COMMENT 开头可在源代码管理的所有代码中唯一地标识查询。

以下查询使用动态管理视图按标签进行搜索：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查询时，必须以方括号或双引号括住文字标签。 Label 是一个保留字，不将其分隔会导致错误。 
> 
> 

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [开发概述](develop-overview.md)。


