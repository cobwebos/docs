---
title: Azure Cosmos DB 查询语言登录
description: 了解 Azure Cosmos DB 中的日志 SQL 系统函数以返回指定数值表达式的自然对数
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ae7812670da836efa326b9224547e4d1b64374c2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873278"
---
# <a name="log-azure-cosmos-db"></a>日志（Azure Cosmos DB）
 返回指定数值表达式的自然对数。  
  
## <a name="syntax"></a>语法
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
base  
   设置对数底数的可选数值参数。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="remarks"></a>备注
  
  默认情况下，LOG() 返回自然对数。 可以使用可选底数参数将对数的底数改为另一个值。  
  
  自然对数是以 e 为底数的对数，其中 e 是一个约等于2.718281828 的无理数常数。  
  
  某个数的指数的自然对数就是该数本身：LOG( EXP( n ) ) = n。 并且某个数的自然对数的指数也是该数字本身：EXP( LOG( n ) ) = n。  
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回指定变量 (10) 的对数值。  
  
```sql
SELECT LOG(10) AS log  
```  
  
 结果集如下。  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 以下示例计算某数指数的 `LOG`。  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 结果集如下。  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
