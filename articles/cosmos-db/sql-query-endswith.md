---
title: Azure Cosmos DB 查询语言中的 EndsWith
description: 了解 Azure Cosmos DB 中的 ENDSWITH SQL 系统函数如何返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0cd927af50eca04aa8162d9d8f292077d9e4165c
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844958"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。  
  
## <a name="syntax"></a>语法
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>参数
  
str_expr1  
   一个字符串表达式。  
  
str_expr2  
   要与 str_expr1 的结尾进行比较的字符串表达式。

*bool_expr* 忽略大小写的可选值。 如果设置为 true，则 ENDSWITH 将执行不区分大小写的搜索。 如果未指定，则此值为 false。
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
以下示例检查字符串“abc”是否以“b”和“bC”结尾。  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 下面是结果集：  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

EndsWith 的 RU 消耗将随着系统函数中属性的基数的增加而增加。 换言之，如果要检查某个属性值是否以特定字符串结尾，则查询 RU 费用将取决于该属性的可能值数量。

例如，考虑两个属性：town 和 country。 town 的基数是 5,000，country 的基数是 200。 下面展示了两个示例查询：

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

第一个查询可能比第二个查询使用更多的 RU，因为 town 的基数高于 country 的基数。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
