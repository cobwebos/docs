---
title: 用 Synapse SQL 赋值变量
description: 本文介绍了如何通过 Synapse SQL 分配 T-sql 变量。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c3bd04d037f7852bd7d9ea51719abf27f09093b0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495669"
---
# <a name="assign-variables-with-synapse-sql"></a>用 Synapse SQL 赋值变量

本文介绍了如何通过 Synapse SQL 分配 T-sql 变量。

## <a name="set-variables-with-declare"></a>使用 DECLARE 设置变量

Synapse SQL 中的变量是使用 `DECLARE` 语句或语句设置的 `SET` 。 使用 DECLARE 初始化变量是在 Synapse SQL 中设置变量值的最灵活方式之一。

```sql
DECLARE @v  int = 0
;
```

还可以使用 DECLARE 一次性设置多个变量。 不能使用 SELECT 或 UPDATE 执行以下操作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

不能在同一 DECLARE 语句中初始化和使用变量。 为了说明这一点，不允许使用下面的示例，因为* \@ p1*是在同一 DECLARE 语句中初始化和使用的。 下面的示例会出错。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>使用 SET 来设置值

SET 是设置单个变量的常见方法。

以下语句是使用 SET 设置变量的有效方法：

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

一次只能使用 SET 设置一个变量。 但是可使用复合运算符。

## <a name="limitations"></a>限制

不能使用 UPDATE 来分配变量。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[SYNAPSE SQL 开发概述](develop-overview.md)一文。
