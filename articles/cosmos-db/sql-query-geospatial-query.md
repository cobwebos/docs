---
title: 使用 Azure 宇宙数据库查询地理空间数据
description: 使用 Azure 宇宙数据库查询空间数据
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566317"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>使用 Azure 宇宙数据库查询地理空间数据

本文将介绍如何使用 SQL 和 LINQ 在 Azure Cosmos DB 中查询地理空间数据。 目前仅支持 Azure Cosmos DB SQL API 帐户存储和访问地理空间数据。 Azure Cosmos DB 支持以下用于查询地理空间的开放地理空间信息联盟 (OGC) 内置函数。 有关 SQL 语言中完整的内置函数集的详细信息，请参阅[Azure Cosmos DB 中的查询系统函数](sql-query-system-functions.md)。

## <a name="spatial-sql-built-in-functions"></a>空间 SQL 内置函数

下面是可用于在 Azure Cosmos DB 中查询的地理空间系统函数列表：

|**使用**|**说明**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | 返回两个 GeoJSON 点、多边形或 LineString 表达式之间的距离。|
|ST_WITHIN (spatial_expr, spatial_expr) | 返回一个布尔表达式，指示第一个 GeoJSON 对象（点、多边形或 LineString）是否在第二个 GeoJSON 对象 （点、多边形或 LineString）内。|
|ST_INTERSECTS (spatial_expr, spatial_expr)| 返回一个布尔表达式，指示两个指定的 GeoJSON 对象 （点、多边形或 LineString）是否相交。|
|ST_ISVALID| 返回一个布尔值，指示指定的 GeoJSON 点、多边形或 LineString 表达式是否有效。|
| ST_ISVALIDDETAILED| 如果指定的 GeoJSON 点、多边形或 LineString 表达式有效，则返回包含布尔值的 JSON 值。 如果无效，它将返回原因作为字符串值。|

空间函数可用于对空间数据执行邻近查询。 例如，下面是一个查询，它使用`ST_DISTANCE`内置函数返回距离指定位置 30 公里以内的所有族文档。

**查询**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**结果**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

如果在索引策略中包含空间索引，则将通过索引有效地进行“距离查询”。 有关空间索引的详细信息，请参阅[地理空间索引](sql-query-geospatial-index.md)。 如果没有指定路径的空间索引，则查询将执行容器的扫描。

`ST_WITHIN`可用于检查点是否位于多边形内。 多边形通常用来表示边界，例如邮政编码、省/自治区边界或自然构成物。 再次说明，如果在索引策略中包含空间索引，则将通过索引有效地进行“within”查询。

中的`ST_WITHIN`多边形参数只能包含一个环，也就是说，多边形不能包含其中的孔。

**查询**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**结果**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> 与 Azure Cosmos DB 查询中不匹配类型的工作方式类似，如果任一参数中指定的位置值格式不正确或无效，则会评估为“未定义”****，并且会在查询结果中跳过已评估的文档。 如果查询未返回任何结果，请`ST_ISVALIDDETAILED`运行以调试空间类型无效的原因。
>
>

Azure Cosmos DB 还支持执行反向查询，即可在 Azure Cosmos DB 中索引多边形或线，并查询包含指定点的区域。 这种模式通常在物流中用于识别如卡车进入或离开指定区域的时间等情况。

**查询**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**结果**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`并`ST_ISVALIDDETAILED`可用于检查空间对象是否有效。 例如，下列查询检查纬度值 (-132.8) 超出范围的点的有效性。 `ST_ISVALID`仅返回布尔值，并`ST_ISVALIDDETAILED`返回布尔值和包含其被视为无效原因的字符串。

**查询**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**结果**

```json
    [{
      "$1": false
    }]
```

这些函数也可以用来验证多边形。 例如，在这里，我们用于`ST_ISVALIDDETAILED`验证未关闭的多边形。

**查询**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**结果**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>.NET SDK 中的 LINQ 查询

SQL .NET SDK 还提供存根方法 `Distance()` 和 `Within()`，供用户在 LINQ 表达式中使用。 SQL LINQ 提供程序会将这些方法调用转换为等效的 SQL 内置函数调用（分别为 ST_DISTANCE 和 ST_WITHIN）。

下面是 LINQ 查询的示例，该查询使用 LINQ 查找 Azure Cosmos`location`容器中的值在指定点半径 30 km 范围内的所有文档。

**LINQ 距离查询**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

同样，下面是查找其`location`位于指定框/多边形内的所有文档的查询。

**LINQ Within 查询**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>后续步骤

已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 深入了解 [Azure Cosmos DB 查询](sql-query-getting-started.md)
* 了解有关[Azure 宇宙 DB 中的地理空间和地理JSON 位置数据](sql-query-geospatial-intro.md)
* 使用[Azure 宇宙数据库了解有关索引空间数据](sql-query-geospatial-index.md)的更多信息
