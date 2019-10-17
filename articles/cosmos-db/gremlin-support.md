---
title: Azure Cosmos DB Gremlin 支持
description: 了解 Apache TinkerPop 的 Gremlin 语言。 了解 Azure Cosmos DB 中提供了哪些功能和步骤
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 0c1ca054f9d28bb81c6f8acf6c0f43b134a596ed
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293786"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Azure Cosmos DB Gremlin 图形支持
Azure Cosmos DB 支持 [Apache Tinkerpop](https://tinkerpop.apache.org) 的图形遍历语言（称为 [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)）。 可以使用 Gremlin 语言创建图形实体（顶点和边缘）、修改这些实体内部的属性、执行查询和遍历，以及删除实体。 

本文提供 Gremlin 的快速演练，并列举 Gremlin API 支持的 Gremlin 功能。

## <a name="compatible-client-libraries"></a>兼容的客户端库

下表显示可以对 Azure Cosmos DB 使用的常用 Gremlin 驱动程序：

| 下载 | 源 | 入门 | 支持的连接器版本 |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [GitHub 上的 Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [使用 .NET 创建图形](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [使用 Java 创建图形](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [GitHub 上的 Gremlin-JavaScript](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [使用 Node.js 创建图形](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [GitHub 上的 Gremlin-Python](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [使用 Python 创建图形](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [GitHub 上的 Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [使用 PHP 创建图形](create-graph-php.md) | 3.1.0 |
| [Gremlin 控制台](https://tinkerpop.apache.org/downloads.html) | [TinkerPop 文档](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [使用 Gremlin 控制台创建图形](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>支持的图对象
TinkerPop 是涵盖多种图形技术的标准。 因此，它使用标准的术语来描述图形提供程序提供的功能。 Azure Cosmos DB 提供一个可跨多个服务器或群集分区的持久性、高并发性、可写的图形数据库。 

下表列出了 Azure Cosmos DB 实现的 TinkerPop 功能： 

| 类别 | Azure Cosmos DB 实现 |  说明 | 
| --- | --- | --- |
| 图形功能 | 提供持久性和并发访问。 旨在支持事务 | 可通过 Spark 连接器实现计算机方法。 |
| 变量功能 | 支持布尔值、整数、字节、双精度值、浮点值、长整数和字符串 | 支持基元类型，通过数据模型与复杂类型兼容 |
| 顶点功能 | 支持 RemoveVertices、MetaProperties、AddVertices、MultiProperties、StringIds、UserSuppliedIds、AddProperty、RemoveProperty  | 支持创建、修改和删除顶点 |
| 顶点属性功能 | StringIds、UserSuppliedIds、AddProperty、RemoveProperty、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支持创建、修改和删除顶点属性 |
| 边缘功能 | AddEdges、RemoveEdges、StringIds、UserSuppliedIds、AddProperty、RemoveProperty | 支持创建、修改和删除边缘 |
| 边缘属性功能 | Properties、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 支持创建、修改和删除边缘属性 |

## <a name="gremlin-wire-format-graphson"></a>Gremlin 网络格式：GraphSON

从 Gremlin 操作返回结果时，Azure Cosmos DB 使用 [GraphSON 格式](http://tinkerpop.apache.org/docs/current/reference/#graphson)。 Azure Cosmos DB 目前支持“GraphSONv2”版本。 GraphSON 是 Gremlin 标准格式，它使用 JSON 来表示顶点、边缘和属性（单值和多值属性）。

例如，以下代码片段显示了从 Azure Cosmos DB *返回到客户端*的某个顶点的 GraphSON 表示形式。 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

下面介绍 GraphSON 使用的顶点属性：

| 属性 | 说明 | 
| --- | --- | --- |
| `id` | 顶点的 ID。 必须唯一（在适用的情况下，可与 `_partition` 的值合并）。 如果未提供任何值，则系统会自动提供一个包含 GUID 的值 | 
| `label` | 顶点的标签。 此属性用于描述实体类型。 |
| `type` | 用于将顶点与非图形文档相区分 |
| `properties` | 与顶点关联的用户定义属性包。 每个属性可以有多个值。 |
| `_partition` | 顶点的分区键。 用于[图形分区](graph-partitioning.md)。 |
| `outE` | 此属性包含顶点中外部边缘的列表。 存储顶点的相邻信息，以便快速执行遍历。 边缘根据其标签分组。 |

边缘包含以下信息，以方便导航到图形的其他部件。

| 属性 | 说明 |
| --- | --- |
| `id` | 边缘的 ID。 必须唯一（在适用的情况下，可与 `_partition` 的值合并） |
| `label` | 边缘的标签。 此属性是可选的，用于描述关系类型。 |
| `inV` | 此属性包含边缘的一系列顶点。 存储顶点的相邻信息可以快速执行遍历。 顶点根据其标签分组。 |
| `properties` | 与边缘关联的用户定义属性包。 每个属性可以有多个值。 |

每个属性可在一个数组中存储多个值。 

| 属性 | 说明 |
| --- | --- |
| `value` | 属性的值

## <a name="gremlin-steps"></a>Gremlin 的步骤
现在，让我们了解 Azure Cosmos DB 支持的 Gremlin 步骤。 有关 Gremlin 的完整参考信息，请参阅 [TinkerPop 参考](https://tinkerpop.apache.org/docs/3.3.2/reference)。

| 步骤 | Description | TinkerPop 3.2 文档 |
| --- | --- | --- |
| `addE` | 在两个顶点之间添加边缘 | [addE 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | 将顶点添加到图形 | [addV 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | 确保所有遍历都返回值 | [and 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | 用于向步骤的输出分配变量的步骤调制器 | [as 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | 与 `group` 和 `order` 配合使用的步骤调制器 | [by 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | 返回第一个返回结果的遍历 | [coalesce 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | 返回常量值。 与 `coalesce` 配合使用| [constant 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | 从遍历返回计数 | [count 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | 返回已删除重复内容的值 | [dedup 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | 丢弃值（顶点/边缘） | [drop 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | 创建执行的 Gremlin 步骤生成的所有操作的说明 | [executionProfile 步骤](graph-execution-profile.md) |
| `fold` | 充当用于计算结果聚合值的屏障| [fold 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | 根据指定的标签将值分组| [group 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | 用于筛选属性、顶点和边缘。 支持 `hasLabel`、`hasId`、`hasNot` 和 `has` 变体。 | [has 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | 将值注入流中| [inject 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | 用于通过布尔表达式执行筛选器 | [is 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | 用于限制遍历中的项数| [limit 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | 本地包装遍历的某个部分，类似于子查询 | [local 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | 用于生成筛选器的求反结果 | [not 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | 如果生成了某个结果，则返回指定遍历的结果，否则返回调用元素 | [optional 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | 确保至少有一个遍历会返回值 | [or 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | 按指定的排序顺序返回结果 | [order 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | 返回遍历的完整路径 | [path 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | 将属性投影为映射 | [project 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | 返回指定标签的属性 | [properties 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | 根据指定的值范围进行筛选| [range 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | 将步骤重复指定的次数。 用于循环 | [repeat 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | 用于对遍历返回的结果采样 | [sample 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | 用于投影遍历返回的结果 |  [select 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | 用于遍历返回的非阻塞聚合 | [store 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的开头进行匹配 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的结尾进行匹配 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来将某个属性与给定字符串的内容进行匹配 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不以给定字符串开头的属性 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不以给定字符串结尾的属性 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | 字符串筛选函数。 此函数用作 `has()` 步骤的谓词来匹配不包含给定字符串的属性 | [TextP 谓词](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | 将顶点中的路径聚合到树中 | [tree 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | 将迭代器作为步骤展开| [unfold 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | 合并多个遍历返回的结果| [union 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | 包括顶点与边缘之间的遍历所需的步骤：`V`、`E`、`out`、`in`、`both`、`outE`、`inE`、`bothE`、`outV`、`inV`、`bothV` 和 `otherV` | [vertex 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | 用于筛选遍历返回的结果。 支持 `eq`、`neq`、`lt`、`lte`、`gt`、`gte` 和 `between` 运算符  | [where 步骤](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Azure Cosmos DB 提供的写入优化引擎默认支持自动对顶点和边缘中的所有属性编制索引。 因此，使用筛选器、范围查询、排序或聚合对任何属性执行的查询将从索引处理，并可有效完成。 有关 Azure Cosmos DB 中索引编制的工作原理的详细信息，请参阅有关[架构不可知的索引编制](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)的文章。

## <a name="next-steps"></a>后续步骤
* 开始[使用我们的 SDK](create-graph-dotnet.md) 构建图形应用程序 
* 详细了解 Azure Cosmos DB 中的[图形支持](graph-introduction.md)
