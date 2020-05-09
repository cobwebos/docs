---
title: 在 Azure Cosmos DB 存储过程和触发器中使用集成了 JavaScript 的查询 API
description: 本文介绍了有关使用 JavaScript 语言集成式查询 API 在 Azure Cosmos DB 中创建存储过程和触发器的概念。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 785c430347bc62a00eee80c977f2d6ce440c08db
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982269"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 JavaScript 查询 API

除了使用 Azure Cosmos DB 中的 SQL API 发出查询外，[Cosmos DB 服务器端 SDK](https://azure.github.io/azure-cosmosdb-js-server/) 还提供了一个 JavaScript 接口，用于在 Cosmos DB 存储过程和触发器中执行优化查询。 你不需要了解 SQL 语言便可使用此 JavaScript 接口。 JavaScript 查询 API 允许使用与 ECMAScript5 的数组内置项类似的语法以及 Lodash 之类的热门 JavaScript 库，通过将谓词函数传递到函数调用序列中以编程方式生成查询。 查询将由 JavaScript 运行时进行分析并使用 Azure Cosmos DB 索引高效执行。

## <a name="supported-javascript-functions"></a>支持的 JavaScript 函数

| **Function** | **说明** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|发起一个必须用 value() 终止的连锁调用。|
|`filter(predicateFunction [, options] [, callback])`|使用返回 true/false 的谓词函数对输入进行筛选，以便将输入文档筛选出或筛选到结果集。 此函数与 SQL 中的 WHERE 子句行为相似。|
|`flatten([isShallow] [, options] [, callback])`|将每个输入项合且并平展到单个数组。 此函数与 LINQ 中的 SelectMany 行为相似。|
|`map(transformationFunction [, options] [, callback])`|应用给定一个转换函数的投影，该函数将每个输入项映射到 JavaScript 对象或值。 此函数与 SQL 中的 SELECT 子句行为相似。|
|`pluck([propertyName] [, options] [, callback])`|此函数是特定映射的快捷方式，该映射用于从每个输入项提取单个属性的值。|
|`sortBy([predicate] [, options] [, callback])`|通过使用给定的谓词在输入文档流中按升序方式对文档进行排序来生成新的一组文档。 此函数与 SQL 中的 ORDER BY 子句行为相似。|
|`sortByDescending([predicate] [, options] [, callback])`|通过使用给定的谓词在输入文档流中按降序方式对文档进行排序来生成新的一组文档。 此函数与 SQL 中的 ORDER BY x DESC 子句行为相似。|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|执行与内层数组的自联接，并将来自双方的结果作为元组添加到结果投影。 例如，将 person 文档与 person.pets 进行联接将生成 [person, pet] 元组。 这类似于 .NET LINK 中的 SelectMany。|

当其中包含谓词和/或选择器函数时，以下 JavaScript 构造将自动优化以在 Azure Cosmos DB 索引上直接运行：

- 简单运算符：`=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- 文本（包括对象文本）：{}
- var, return

以下 JavaScript 构造不会针对 Azure Cosmos DB 索引进行优化：

- 控制流（例如，if、for、while）
- 函数调用

有关详细信息，请参阅 [Cosmos DB 服务器端 JavaScript 文档](https://azure.github.io/azure-cosmosdb-js-server/)。

## <a name="sql-to-javascript-cheat-sheet"></a>SQL 到 JavaScript 备忘单

下表表示各种不同的 SQL 查询和对应的 JavaScript 查询。 与 SQL 查询一样，属性（例如 item.id）区分大小写。

> [!NOTE]
> 使用 JavaScript 查询 API 时，`__`（双下划线）是 `getContext().getCollection()` 的别名。

|**SQL**|**JavaScript 查询 API**|**说明**|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|结果为所有文档（使用延续令牌分页）保持原样。|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|从所有文档投影 ID、消息（别名为 msg）和操作。|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|查询具有此谓词的文档：id = "X998_Y998"。|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|查询具有 Tags 属性且 Tags 为一个包含值 123 的数组的文档。|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|查询具有谓词 id = "X998_Y998" 的文档，并投影 ID 和消息（别名为 msg）。|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|筛选具有数组属性 Tags 的文档，按 _ts 时间戳系统属性对结果文档进行排序，并投影并平展 Tags 数组。|

## <a name="next-steps"></a>后续步骤

详细了解概念以及如何在 Azure Cosmos DB 中编写并使用存储过程、触发器和用户定义的函数：

- [如何使用 Javascript 查询 API 编写存储过程和触发器](how-to-write-javascript-query-api.md)
- [使用 Azure Cosmos DB 存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)
- [如何在 Azure Cosmos DB 中使用存储过程、触发器和用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript 服务器端 API 参考](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
