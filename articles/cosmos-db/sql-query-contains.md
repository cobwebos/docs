---
title: Azure Cosmos DB 查询语言中的 Contains
description: 了解 Azure Cosmos DB 中的 CONTAINS SQL 系统函数如何返回一个布尔值，指示第一个字符串表达式是否包含第二个字符串表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a08fe47122d7e9ddd1c9038bb5f15ebbb0be30fa
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848968"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)

 返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。  
  
## <a name="syntax"></a>语法
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>参数
  
str_expr1  
   是要搜索的字符串表达式。  
  
str_expr2  
   是要查找的字符串表达式。  

bool_expr 忽略大小写的可选值。 如果设置为 true，CONTAINS 将执行不区分大小写的搜索。 如果未指定，则此值为 false。
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例检查“abc”是否包含“ab”，以及“abc”是否包含“A”。  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 下面是结果集：  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

Contains 的 RU 消耗将随着系统函数中属性的基数的增加而增加。 换言之，如果要检查某个属性值是否包含特定字符串，则查询 RU 费用将取决于该属性的可能值数量。

例如，请考虑两个属性：town 和 country。 town 的基数是 5,000，country 的基数是 200。 下面展示了两个示例查询：

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

第一个查询可能比第二个查询使用更多的 RU，因为 town 的基数高于 country 的基数。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
