---
title: Azure Cosmos DB 查询语言中的 StringToArray
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToArray。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349280"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray （Azure Cosmos DB）
 返回已转换为数组的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
## <a name="syntax"></a>语法
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要分析为 JSON 数组表达式的字符串表达式。 
  
## <a name="return-types"></a>返回类型
  
  返回一个数组表达式或未定义。 
  
## <a name="remarks"></a>备注
  嵌套字符串值必须用双引号编写，才能成为有效的 JSON。 有关 JSON 格式的详细信息，请参阅 [json.org](https://json.org/)
  
## <a name="examples"></a>示例
  
  下面的示例演示在不同类型之间 @no__t 的行为。 
  
 下面是输入有效的示例。

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

结果集如下。

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

下面是输入无效的示例。 
   
 在数组中使用单引号不是有效的 JSON。
即使它们在查询中有效，系统也不会将其解析为有效数组。 必须将数组字符串中的字符串转义为 "[\\"\\"]"，否则其引号必须为单个 '[""]'。

```sql
SELECT
    StringToArray("['5','6','7']")
```

结果集如下。

```json
[{}]
```

下面是输入无效的示例。
   
 传递的表达式将会解析为 JSON 数组；下面的示例不会计算为类型数组，因此返回未定义。
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

结果集如下。

```json
[{}]
```

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
