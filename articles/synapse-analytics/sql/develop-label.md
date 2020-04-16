---
title: 在 SynapsE SQL 中使用查询标签
description: 本文中包括在 Synapse SQL 中使用查询标签的基本提示。
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
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430027"
---
# <a name="use-query-labels-in-synapse-sql"></a>在 SynapsE SQL 中使用查询标签
本文中包括在 Synapse SQL 中使用查询标签的基本提示。

> [!NOTE]
> SQL 按需（预览）不支持标记查询。

## <a name="what-are-query-labels"></a>什么是查询标签
SQL 池支持一个称为查询标签的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记特别有用，因为可以通过 DMV 查询标签。 对标签的查询提供了一种查找问题查询的机制，并有助于通过 ELT 运行识别进度。

良好的命名约定最有帮助。 例如，使用 PROJECT、程序、声明或注释启动标签可唯一标识源代码管理中的所有代码之间的查询。

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
有关更多开发技巧，请参阅[开发概述](develop-overview.md)。


