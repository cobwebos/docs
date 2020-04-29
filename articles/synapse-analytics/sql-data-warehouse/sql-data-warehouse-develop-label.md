---
title: 使用标签检测查询
description: 有关使用标签来检测 Synapse SQL 池中用于开发解决方案的查询的技巧。
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
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633498"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>使用标签检测 Synapse SQL 池中的查询

本文介绍如何使用标签来开发解决方案，以便在 SQL 池中检测查询。

有关在开发解决方案时于 Azure SQL 数据仓库中使用标签检测查询的技巧。

## <a name="what-are-labels"></a>什么是标签？

SQL 池支持称为查询标签的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记很有用，因为可以通过 Dmv 查询标签。

对标签进行查询提供了一种用于定位有问题的查询并帮助查明 ELT 运行进度的机制。

良好的命名约定确实很有帮助。 例如，以 PROJECT、PROCEDURE、语句或注释开始标签会在源代码管理中的所有代码中唯一标识查询。

下面的查询使用动态管理视图按标签进行搜索：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查询时，必须以方括号或双引号括住文字标签。 Label 是一个保留字，不将其分隔会导致错误。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。
