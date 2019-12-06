---
title: Azure Cosmos DB 查询语言中的 ARRAY_SLICE
description: 了解 Azure Cosmos DB 如何返回数组表达式的一部分的数组切片 SQL 系统函数
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3c65886eabfa87f729e18fb854aca6662d1d6fac
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871782"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE （Azure Cosmos DB）
 返回部分数组表达式。
  
## <a name="syntax"></a>语法
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>参数
  
*arr_expr*  
   为任意数组表达式。  
  
*num_expr*  
   用于开始数组的从零开始的数字索引。 负值可用于指定相对于数组最后一个元素的起始索引，即 -1 引用数组中最后一个元素。  

*num_expr*可选的数值表达式，用于设置生成的数组中的最大元素数。    

## <a name="return-types"></a>返回类型
  
  返回数组表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何使用 `ARRAY_SLICE`获取数组的不同切片。  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 结果集如下。  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="next-steps"></a>后续步骤

- [数组函数 Azure Cosmos DB](sql-query-array-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
