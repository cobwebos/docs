---
title: 分配变量
description: 在本文中，你将了解有关在 SQL 池中分配 T-sql 变量的重要提示。
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633404"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>在 Synapse SQL 池中分配变量

在本文中，你将了解有关在 SQL 池中分配 T-sql 变量的重要提示。

## <a name="set-variables-with-declare"></a>用 DECLARE 设置变量

SQL 池中的变量是使用`DECLARE`语句或`SET`语句设置的。 使用 DECLARE 初始化变量是在 SQL 池中设置变量值的最灵活方式之一。

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

不能在同一个 DECLARE 语句中初始化和使用变量。 为了演示要点，**不**允许出现以下示例中的情况，因为同一 DECLARE 语句中初始化和使用了 @p1。 同样，下面的示例提供了一个错误：

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>设置值时设置

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

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。
