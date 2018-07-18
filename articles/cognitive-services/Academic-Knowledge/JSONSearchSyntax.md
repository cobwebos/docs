---
title: 学术知识 API 中的 JSON 搜索语法 | Microsoft Docs
description: 了解可以对 Microsoft 认知服务中的学术知识 API 使用的 JSON 搜索语法。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a4b9cf535dae60258d71c43bba6f9eec1444bd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365389"
---
# <a name="json-search-syntax"></a>JSON 搜索语法

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

查询路径中的节点名称（“v0, v1, ...”）用作可以在查询对象中引用的节点标识符；路径中的边缘名称（“e0, e1, ...”）表示相应边缘的类型。 可以使用星号 * 作为节点名称或边缘名称（必须指定名称的起始节点除外），以声明此类元素没有任何约束。 例如，查询路径 `/v0/*/v1/e1/*/` 从 Graph 中检索路径，而不限制边缘类型（“v0, v1”）。 同时，查询对路径目标（最后一个节点）也没有任何约束。

如果路径中只有一个节点（假设为 v0），查询会直接返回所有符合约束的实体。 应用于起始节点的约束对象称为“起始查询对象”，它的规范如下。

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

如果路径中除了有起始节点外还有其他节点，查询处理器会按照指定路径模式执行 Graph 遍历。 当它到达一个节点时，就会触发用户指定的遍历操作，即是在当前节点停止并返回，还是继续探索 Graph。 如果没有指定遍历操作，将执行默认操作。 对于中间节点，默认操作是继续探索 Graph。 对于路径中的最后一个节点，默认操作是停止并返回。 指定遍历操作的约束对象称为“遍历操作对象”。 它的规范如下：

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

JSON 搜索查询的 POST 正文应至少包含路径模式。 可以视需要添加遍历操作对象。 下面展示了两个示例。

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

