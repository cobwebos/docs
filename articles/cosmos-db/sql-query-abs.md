---
title: Azure Cosmos DB 查询语言中的 ABS
description: 了解 Azure Cosmos DB 中的绝对（ABS） SQL 系统函数如何返回指定数值表达式的正值
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70648dcf28d760c3a81c0cb426cd9b2d3ce96fff
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871850"
---
# <a name="abs-azure-cosmos-db"></a>ABS （Azure Cosmos DB）
 返回指定数值表达式的绝对（正）值。  
  
## <a name="syntax"></a>语法
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例显示了对三个不同数字使用 `ABS` 函数所得的结果。  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 结果集如下。  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
