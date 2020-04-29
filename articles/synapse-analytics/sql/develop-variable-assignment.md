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
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428662"
---
# <a name="assigning-variables-with-synapse-sql"></a>用 Synapse SQL 赋值变量

本文介绍了如何通过 Synapse SQL 分配 T-sql 变量。

## <a name="setting-variables-with-declare"></a>使用 DECLARE 设置变量

Synapse SQL 中的`DECLARE`变量是使用语句或`SET`语句设置的。 使用 DECLARE 初始化变量是在 Synapse SQL 中设置变量值的最灵活方式之一。

```sql
DECLARE @v  int = 0
;
```

还可以使用 DECLARE 一次性设置多个变量。 不能使用 SELECT 或 UPDATE 来执行以下操作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

不能在同一个 DECLARE 语句中初始化和使用变量。 为了说明这一点，不允许使用下面*@p1*的示例，因为在同一个 DECLARE 语句中初始化并使用了。 下面的示例会出错。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>使用 SET 设置值

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

不能对变量赋值使用 UPDATE。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[SYNAPSE SQL 开发概述](develop-overview.md)一文。