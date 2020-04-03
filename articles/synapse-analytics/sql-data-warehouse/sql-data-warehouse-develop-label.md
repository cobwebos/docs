---
title: 使用标签检测查询
description: 使用标签在 Synapse SQL 池中检测查询以开发解决方案的提示。
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
ms.openlocfilehash: c1a4ffcab3d10f1dc91ce036e995ae0026a0d718
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619018"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>使用标签在 Synapse SQL 池中检测查询
本文包括使用标签在 SQL 池中检测查询开发解决方案的提示。


## <a name="what-are-labels"></a>什么是标签？
SQL 池支持一个称为查询标签的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记很有用，因为标签可通过 DMV 进行查询。 

对标签进行查询提供了一种用于定位有问题的查询并帮助查明 ELT 运行进度的机制。

良好的命名约定确实很有帮助。 例如，使用 PROJECT、程序、声明或注释启动标签可唯一标识源代码管理中的所有代码之间的查询。

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
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。


