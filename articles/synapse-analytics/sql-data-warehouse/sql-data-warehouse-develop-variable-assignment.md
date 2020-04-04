---
title: 分配变量
description: 在本文中，您将找到在 SQL 池中分配 T-SQL 变量的基本提示。
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633404"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>在 Synapse SQL 池中分配变量

在本文中，您将找到在 SQL 池中分配 T-SQL 变量的基本提示。

## <a name="set-variables-with-declare"></a>使用 DECLARE 设置变量

SQL 池中的变量使用`DECLARE`语句或`SET`语句进行设置。 使用 DECLARE 初始化变量是在 SQL 池中设置变量值的最灵活方法之一。

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

不能在同一 DECLARE 语句中初始化和使用变量。 为了演示要点，**不**允许出现以下示例中的情况，因为同一 DECLARE 语句中初始化和使用了 @p1。 因此，以下示例给出错误：

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>使用 SET 设置值

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

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。
