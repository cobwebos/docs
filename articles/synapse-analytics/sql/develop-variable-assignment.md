---
title: 使用 Synapse SQL 分配变量
description: 在本文中，您将找到使用 Synapse SQL 分配 T-SQL 变量的提示。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428662"
---
# <a name="assigning-variables-with-synapse-sql"></a>使用 Synapse SQL 分配变量

在本文中，您将找到使用 Synapse SQL 分配 T-SQL 变量的提示。

## <a name="setting-variables-with-declare"></a>使用 DECLARE 设置变量

Synapse SQL 中的变量使用`DECLARE`语句或`SET`语句进行设置。 使用 DECLARE 初始化变量是在 Synapse SQL 中设置变量值的最灵活方法之一。

```sql
DECLARE @v  int = 0
;
```

还可以使用 DECLARE 一次性设置多个变量。 不能使用 SELECT 或更新执行以下操作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

不能在同一 DECLARE 语句中初始化和使用变量。 为了说明这一点，不允许使用以下示例，*@p1*因为在同一 DECLARE 语句中同时初始化和使用。 下面的示例会出错。

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

不能将 UPDATE 用于变量分配。

## <a name="next-steps"></a>后续步骤

有关更多开发提示，请参阅[Synapse SQL 开发概述](develop-overview.md)一文。