---
title: Azure Cosmos DB 的 SQL 联接查询
description: 了解如何联接 Azure Cosmos DB 中的多个表来查询数据
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871136"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB 中的联接

在关系数据库中，跨表联接是设计规范化架构的逻辑必然结果。 与此相反，SQL API 使用无模式项的非规范化数据模型，这是*自联接*的逻辑等效项。

内联会导致加入联接的集产生完整叉积。 N 向联接的结果是获得一组 N-元素元组，其中元组中的每个值与参与联接的别名集相关联，并且可以通过引用其他子句中的这些别名来访问。

## <a name="syntax"></a>语法

该语言支持语法 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查询返回一组具有 `N` 值的元组。 每个元组拥有通过对它们相应的集遍历所有容器别名所产生的值。 

请看以下 FROM 子句：`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 让每个源定义 `input_alias1, input_alias2, …, input_aliasN`。 此 FROM 子句将返回一组 N 元组（带有 N 个值的元组）。 每个元组拥有通过对它们相应的集遍历所有容器别名所产生的值。  
  
示例 1 - 2 个源  
  
- 让 `<from_source1>` 的范围为容器，并表示集 {A, B, C}。  
  
- 让 `<from_source2>` 作为文档范围的引用 input_alias1，表示的集如下所示：  
  
    若 `input_alias1 = A,`，表示集 {1, 2}  
  
    若 `input_alias1 = B,`，表示集 {3}  
  
    若 `input_alias1 = C,`，表示集 {4, 5}  
  
- FROM 子句 `<from_source1> JOIN <from_source2>` 将产生以下元组：  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
示例 2 - 3 个源  
  
- 让 `<from_source1>` 的范围为容器，并表示集 {A, B, C}。  
  
- 让 `<from_source2>` 作为文档范围的引用 `input_alias1`，表示的集如下所示：  
  
    若 `input_alias1 = A,`，表示集 {1, 2}  
  
    若 `input_alias1 = B,`，表示集 {3}  
  
    若 `input_alias1 = C,`，表示集 {4, 5}  
  
- 让 `<from_source3>` 作为文档范围的引用 `input_alias2`，表示的集如下所示：  
  
    若 `input_alias2 = 1,`，表示集 {100, 200}  
  
    若 `input_alias2 = 3,`，表示集 {300}  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 将产生以下元组：  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > `input_alias1` 和 `input_alias2` 的其他值缺少元组，`<from_source3>` 不对其返回任何值。  
  
示例 3 - 3 个源  
  
- 让 <from_source1> 的范围为容器，并表示集 {A, B, C}。  
  
- 让 `<from_source1>` 的范围为容器，并表示集 {A, B, C}。  
  
- <from_source2> 作为引用 input_alias1 的文档作用域，并表示集：  
  
    若 `input_alias1 = A,`，表示集 {1, 2}  
  
    若 `input_alias1 = B,`，表示集 {3}  
  
    若 `input_alias1 = C,`，表示集 {4, 5}  
  
- 让 `<from_source3>` 的范围限定为 `input_alias1`，表示的集如下所示：  
  
    若 `input_alias2 = A,`，表示集 {100, 200}  
  
    若 `input_alias2 = C,`，表示集 {300}  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 将产生以下元组：  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > 这会导致 `<from_source2>` 和 `<from_source3>` 产生叉积，因为两者的范围都为 `<from_source1>`。  这会产生 4 (2x2) 个具有 A 值的元组、0 个具有 B (1x0) 值的元组、和 2 (2x1) 个具有 C 值的元组。  
  
## <a name="examples"></a>示例

下面的示例演示了 JOIN 子句是如何工作的。 在运行这些示例之前，请上传示例[系列数据](sql-query-getting-started.md#upload-sample-data)。 在下面的示例中，由于源中每个项的叉积和空集为空，因此结果为空：

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

结果为：

```json
    [{
    }]
```

在下面的示例中，联接是两个 JSON 对象之间的叉积，项根 `id` 和 `children` subroot。 `children` 是数组在联接中是无效的，因为它处理的是 `children` 数组的一个根。 结果只包含两个结果，因为每个具有数组的项的叉积仅生成一个项。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

其结果是：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

下面的示例演示了更传统的联接：

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

其结果是：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

联接子句源是一个迭代器。 因此，前面的示例中的流为：  

1. 展开数组中 `c` 的每个子元素。
2. 将叉积与第一步所平展 `c` 每个子元素的项的根 `f`。
3. 最后，单独投影根对象 `f` `id` 属性。

第一项 `AndersenFamily`仅包含一个 `children` 元素，因此结果集仅包含单个对象。 第二项 `WakefieldFamily`包含两 `children`，因此叉积产生两个对象，分别对应于每个 `children` 元素。 这两个项中的根字段会是相同的，正如在叉积中所预期的一样。

联接子句的真正实用工具是，在其他难以投影的形状中形成来自叉积的元组。 下面的示例筛选了元组的组合，该组合使用户可以选择由元组整体满足的条件。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

其结果是：

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

前面示例的以下扩展将执行双联接。 可以查看叉积作为以下伪代码：

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` 有一个具有一只宠物的孩子，因此叉积会从该系列生成一行（1\*1\*1）。 `WakefieldFamily` 具有两个子项，其中只有一个子项具有宠物，但孩子有两个宠物。 此系列的叉积产生 1\*1\*2 = 2 行。

在下一个示例中，有一个针对 `pet`的附加筛选器，它排除宠物名称不 `Shadow`的所有元组。 可以从数组中生成元组，筛选元组的任何元素，并投影元素的任何组合。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

其结果是：

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [子查询](sql-query-subquery.md)