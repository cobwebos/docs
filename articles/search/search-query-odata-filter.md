---
title: OData 筛选器引用
titleSuffix: Azure Cognitive Search
description: 用于在 Azure 认知搜索查询中创建筛选表达式的 OData 语言参考和完整语法。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282882"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure 中的 OData $filter 语法认知搜索

除了全文搜索词外，Azure 认知搜索使用[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)将其他条件应用于搜索查询。 本文详细介绍了筛选器的语法。 有关筛选器的定义以及如何使用筛选器来实现特定查询方案的更多常规信息，请参阅[Azure 中的筛选器认知搜索](search-filters.md)。

## <a name="syntax"></a>语法

OData 语言中的筛选器是一个布尔表达式，该表达式又可以是几种类型的表达式之一，如下面的 EBNF （[扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）所示：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

布尔表达式的类型包括：

- 使用 `any` 或 `all`的集合筛选器表达式。 这些将筛选条件应用于集合字段。 有关详细信息，请参阅[Azure 认知搜索中的 OData 集合运算符](search-query-odata-collection-operators.md)。
- 使用运算符（`and`、`or`和 `not`）组合其他布尔表达式的逻辑表达式。 有关详细信息，请参阅[Azure 认知搜索中的 OData 逻辑运算符](search-query-odata-logical-operators.md)。
- 比较表达式，使用运算符 `eq`、`ne`、`gt`、`lt`、`ge`和 `le`比较字段或范围变量与常量值。 有关详细信息，请参阅[Azure 认知搜索中的 OData 比较运算符](search-query-odata-comparison-operators.md)。 比较表达式还用于比较地理空间坐标与使用 `geo.distance` 函数之间的距离。 有关详细信息，请参阅[Azure 认知搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。
- 布尔值文本 `true` 和 `false`。 在以编程方式生成筛选器时，这些常量可能很有用，但在实际情况下不会使用。
- 调用布尔函数，包括：
  - `geo.intersects`，用于测试给定点是否在给定多边形内。 有关详细信息，请参阅[Azure 认知搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。
  - `search.in`，它将字段或范围变量与值列表中的每个值进行比较。 有关详细信息，请参阅[Azure 中的 OData `search.in` 函数认知搜索](search-query-odata-search-in-function.md)。
  - `search.ismatch` 和 `search.ismatchscoring`，用于在筛选器上下文中执行全文搜索操作。 有关详细信息，请参阅[Azure 认知搜索中的 OData 全文搜索功能](search-query-odata-full-text-search-functions.md)。
- `Edm.Boolean`类型的字段路径或范围变量。 例如，如果索引中有一个名为 `IsEnabled` 的布尔字段，并且您希望返回 `true`此字段的所有文档，则筛选表达式可以是 `IsEnabled`的名称。
- 括号中的布尔表达式。 使用括号可以帮助显式确定筛选器中的操作顺序。 有关 OData 运算符的默认优先级的详细信息，请参阅下一节。

### <a name="operator-precedence-in-filters"></a>筛选器中的运算符优先级

如果编写的筛选表达式的子表达式两侧没有括号，Azure 认知搜索会根据一组运算符优先规则对其进行评估。 这些规则基于用于组合子表达式的运算符。 下表按从高到低的顺序列出了运算符组：

| 组 | 运算符 |
| --- | --- |
| 逻辑运算符 | `not` |
| 比较运算符 | `eq`、`ne`、`gt`、`lt`、`ge`、`le` |
| 逻辑运算符 | `and` |
| 逻辑运算符 | `or` |

上表中较高位置的运算符 "与其他运算符相比，将更紧密地绑定到其操作数"。 例如，`and` 的优先级高于 `or`，比较运算符的优先级高于其中任何一个运算符，因此下面两个表达式是等效的：

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` 运算符的优先级最高，甚至比比较运算符高。 这就是尝试编写如下筛选器的原因：

    not Rating gt 5

你会收到以下错误消息：

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

发生此错误的原因是，运算符仅与 `Edm.Int32`类型的 `Rating` 字段相关联，而不是与整个比较表达式相关联。 解决方法是将 `not` 的操作数置于括号中：

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>筛选器大小限制

可以发送到 Azure 认知搜索的筛选器表达式的大小和复杂性有限制。 限制大致基于筛选器表达式中的子句数。 一种很好的原则是，如果你有数百个子句，你会面临超出限制的风险。 建议设计应用程序，使其不会生成未绑定大小的筛选器。

> [!TIP]
> 使用[`search.in` 函数](search-query-odata-search-in-function.md)，而不是长 disjunctions 的相等比较有助于避免筛选子句限制，因为函数调用计为单个子句。

## <a name="examples"></a>示例

查找至少一个房间低于或高于4的基本费率低于 $200 的所有酒店：

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

查找 "海平面 View Motel" 以外的所有酒店，因为2010：

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

查找在2010或更高版本中翻新的所有酒店。 Datetime 文本包含太平洋标准时间的时区信息：  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

查找所有包含停车的酒店，所有房间都不吸烟：

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 或 -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

查找等级为豪华或包含停车场且评分为 5 分的所有酒店：  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

在至少一个房间内查找标记为 "wifi" 的所有酒店（其中每个会议室都有在 `Collection(Edm.String)` 字段中存储的标记）：  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

查找任何会议室的所有酒店：  

    $filter=Rooms/any()

查找所有没有会议室的酒店：

    $filter=not Rooms/any()

查找给定引用点的10公里内的所有酒店（其中 `Location` 是 `Edm.GeographyPoint`类型的字段）：

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

查找给定视区中被描述为多边形的所有酒店（其中 `Location` 是 GeographyPoint 类型的字段）。 必须关闭多边形，这意味着第一个和最后一个点集必须相同。 此外，[点必须以逆时针顺序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

查找 "说明" 字段为 null 的所有酒店。 如果从未设置此字段，则该字段将为 null; 如果已显式设置为 null，则为 null：  

    $filter=Description eq null

查找名称等于 "海平面 View motel" 或 "预算酒店" 的所有酒店。 这些短语包含空格，空格是默认分隔符。 您可以使用单引号指定替代分隔符作为第三个字符串参数：  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找名称等于 "海平面 View motel" 或 "预算酒店" 的所有酒店，用 "|" 分隔：  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找所有房间都具有标记 "wifi" 或 "浴盆" 的所有酒店：

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

查找集合中的短语匹配项，如 "加热式的纸巾架" 或标记中的 "hairdryer 包含"。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

查找包含“waterfront”一词的文档。 此筛选器查询与包含 [ 的](https://docs.microsoft.com/rest/api/searchservice/search-documents)搜索请求`search=waterfront`相同。

    $filter=search.ismatchscoring('waterfront')

查找带有“hostel”一词且评分大于或等于 4 分的文档，或带有“motel”一词且评分等于 5 分的文档。 无法将此请求表达 `search.ismatchscoring` 函数，因为它将全文搜索与使用 `or`的筛选器操作组合在一起。

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

查找没有“luxury”一词的文档。

    $filter=not search.ismatch('luxury')

查找包含短语“ocean view”或评分等于 5 分的文档。 `search.ismatchscoring` 查询仅针对 `HotelName` 和 `Description` 字段执行。 将返回仅与析取的第二子句匹配的文档--`Rating` 等于5的酒店。 这些文档将返回等于零的评分，使其清楚地表明它们与表达式的任何计分部分都不匹配。

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

查找 "酒店" 和 "机场" 条款在说明中相隔不超过五个词的酒店，并且所有房间均为非吸烟。 此查询使用[完整 Lucene 查询语言](query-lucene-syntax.md)。

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
