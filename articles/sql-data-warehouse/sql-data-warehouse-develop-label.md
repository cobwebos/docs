---
title: 在 SQL 数据仓库中使用标签检测查询 | Microsoft Docs
description: 有关在开发解决方案时于 Azure SQL 数据仓库中使用标签检测查询的技巧。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 22737faa146d83f1f31489125dee4146c7d11ac1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524238"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>在 Azure SQL 数据仓库中使用标签检测查询
有关在开发解决方案时于 Azure SQL 数据仓库中使用标签检测查询的技巧。


## <a name="what-are-labels"></a>什么是标签？
SQL 数据仓库支持称为查询标签的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记特别有用，因为可以通过 DMV 查询标签。 对标签进行查询提供了一种用于定位有问题的查询并帮助查明 ELT 运行进度的机制。

良好的命名约定确实很有帮助。 例如，让标签以 PROJECT、PROCEDURE、STATEMENT 或 COMMENT 开头有助于在源代码管理的几乎所有代码中唯一地标识查询。

以下查询使用动态管理视图按标签进行搜索。

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
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。


