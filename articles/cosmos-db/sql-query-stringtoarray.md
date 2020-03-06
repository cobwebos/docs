---
title: Azure Cosmos DB 查询语言中的 StringToArray
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToArray。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302910"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray （Azure Cosmos DB）
 返回转换为数组的表达式。 如果 expression 无法转换，则返回 undefined。  
  
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
  嵌套字符串值必须用双引号编写，才能成为有效的 JSON。 有关 JSON 格式的详细信息，请参阅[json.org](https://json.org/)
  
## <a name="examples"></a>示例
  
  下面的示例演示了 `StringToArray` 跨不同类型的行为。 
  
 下面是有效输入的示例。

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

下面是结果集：

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

下面是无效输入的示例。 
   
 数组中的单引号不是有效的 JSON。
即使它们在查询中是有效的，它们也不会分析为有效的数组。 必须将数组字符串中的字符串转义为 "[\\"\\"]"，或者周围的引号必须是单 "[" "]"。

```sql
SELECT
    StringToArray("['5','6','7']")
```

下面是结果集：

```json
[{}]
```

下面是无效输入的示例。
   
 传递的表达式将被分析为 JSON 数组;下面的计算结果不是类型数组，因此返回 undefined。
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

下面是结果集：

```json
[{}]
```

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
