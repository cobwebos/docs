---
title: Azure Cosmos DB 查询语言中的 StringToObject
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToObject。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296375"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject （Azure Cosmos DB）
 返回转换为对象的表达式。 如果 expression 无法转换，则返回 undefined。  
  
## <a name="syntax"></a>语法
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要分析为 JSON 对象表达式的字符串表达式。 请注意，必须用双引号编写嵌套字符串值才能有效。 有关 JSON 格式的详细信息，请参阅[json.org](https://json.org/)  
  
## <a name="return-types"></a>返回类型
  
  返回对象表达式或未定义。  
  
## <a name="examples"></a>示例
  
  下面的示例演示了 `StringToObject` 跨不同类型的行为。 
  
 下面是有效输入的示例。

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

下面是结果集：

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 下面是无效输入的示例。
即使它们在查询中是有效的，它们也不会分析为有效的对象。 对象字符串中的字符串必须转义 "{\\"\\"：\\" str\\"}" 或周围的引号必须是 "{" a "：" str "}"。

属性名称前后的单引号不是有效的 JSON。

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

下面是结果集：

```json
[{}]
```  

不带引号的属性名不是有效的 JSON。

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

下面是结果集：

```json
[{}]
``` 

下面是无效输入的示例。

 传递的表达式将被分析为 JSON 对象;这些输入的计算结果不是类型 object，因此返回 undefined。

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
