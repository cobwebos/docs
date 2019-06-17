---
title: OData 筛选器引用的 Azure 搜索
description: 在 Azure 搜索查询中的筛选器语法的 OData 语言参考。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079907"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Azure 搜索中的 OData $filter 语法

Azure 搜索使用[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)除了全文搜索词的搜索查询应用其他条件。 本文介绍的语法的详细信息中的筛选器。 有关筛选器是什么以及如何使用它们来实现特定的查询方案的更多常规信息，请参阅[Azure 搜索中的筛选器](search-filters.md)。

## <a name="syntax"></a>语法

OData 语言中的筛选器是又可以是几种类型的表达式，如以下 EBNF 所示的布尔表达式 ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

布尔表达式的类型包括：

- 使用集合筛选器表达式`any`或`all`。 这些应用于集合字段的筛选条件。 有关详细信息，请参阅[Azure 搜索中的 OData 集合运算符](search-query-odata-collection-operators.md)。
- 将其他布尔表达式使用运算符组合的逻辑表达式`and`， `or`，和`not`。 有关详细信息，请参阅[OData 在 Azure 搜索中的逻辑运算符](search-query-odata-logical-operators.md)。
- 比较表达式，其比较字段或范围变量与使用运算符的常数值`eq`， `ne`， `gt`， `lt`， `ge`，并`le`。 有关详细信息，请参阅[Azure 搜索中的 OData 比较运算符](search-query-odata-comparison-operators.md)。 比较表达式还用于比较使用地理空间坐标之间的距离`geo.distance`函数。 有关详细信息，请参阅[Azure 搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。
- 布尔值文字`true`和`false`。 这些常量时非常有用有时以编程方式生成筛选器，但否则往往不会在实践中使用。
- 对布尔函数，其中包括：
  - `geo.intersects`用于测试是否位于给定的多边形内的给的定点。 有关详细信息，请参阅[Azure 搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。
  - `search.in`其中比较值的列表中每个值的字段或范围变量。 有关详细信息，请参阅[OData`search.in`在 Azure 搜索中的函数](search-query-odata-search-in-function.md)。
  - `search.ismatch` 和`search.ismatchscoring`，其在筛选器的上下文中执行全文搜索操作。 有关详细信息，请参阅[OData 在 Azure 搜索中全文搜索函数](search-query-odata-full-text-search-functions.md)。
- 字段路径或范围类型的变量`Edm.Boolean`。 例如，如果索引具有一个名为的布尔字段`IsEnabled`并且你想要返回所有文档，则此字段`true`，筛选器表达式可以只是名称`IsEnabled`。
- 括号中的布尔表达式。 使用括号可以帮助显式确定筛选器中的操作的顺序。 默认优先级的 OData 运算符的详细信息，请参阅下一节。

### <a name="operator-precedence-in-filters"></a>筛选器中的运算符优先级

如果您编写具有其子表达式没有括号的筛选器表达式，Azure 搜索将评估它根据一组的运算符优先级规则。 这些规则基于哪些运算符用于组合的子表达式。 下表列出了从最高优先级到最低优先级的顺序的运算符的组：

| 组 | 运算符 |
| --- | --- |
| 逻辑运算符 | `not` |
| 比较运算符 | `eq`、`ne`、`gt`、`lt`、`ge`、`le` |
| 逻辑运算符 | `and` |
| 逻辑运算符 | `or` |

上表中较高的运算符将"绑定更紧密地"到它比其他运算符的操作数。 例如，`and`的优先级高于`or`，、 比较运算符的优先级高于任一它们，因此以下两个表达式是等效的：

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not`运算符具有最高优先级的所有-甚至高于比较运算符。 这就是为什么如果尝试编写此类筛选器：

    not Rating gt 5

你将收到此错误消息：

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

因为该运算符与相关联，会发生此错误仅`Rating`字段中，其类型`Edm.Int32`，而不是与整个比较表达式。 解决方法是设置的操作数`not`在括号中：

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>筛选器大小限制

可以发送到 Azure 搜索的筛选表达式的大小和复杂性存在限制。 限制大致基于筛选器表达式中的子句数。 比较好的原则是，如果有数百个子句，您就处于超过限制的风险。 我们建议设计的方式，它不会生成大小不受限制的筛选器的应用程序。

> [!TIP]
> 使用[`search.in`函数](search-query-odata-search-in-function.md)而不是长表达式的相等性比较可以帮助避免筛选器子句限制，因为函数调用计为一个子句。

## <a name="examples"></a>示例

查找所有酒店至少一个房间基本费率小于 200 美元信用额度，并且评级为在 4 或更高：

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

找到自 2010年以来已翻新的"Sea 视图 Motel"以外的所有酒店：

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

查找所有在 2010年或更高版本翻新的酒店。 日期时间文字包含太平洋标准时间的时区信息：  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

查找包含停车且所有聊天室都的非吸烟的所有酒店：

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 或 -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

查找等级为豪华或包含停车场且评分为 5 分的所有酒店：  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

至少一个房间中查找具有标记"wifi"的所有酒店 (其中每个房间具有标记存储在`Collection(Edm.String)`字段):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

查找与任何聊天室的所有酒店：  

    $filter=Rooms/any()

查找没有聊天室的所有酒店：

    $filter=not Rooms/any()

找到的与给定的参照点相距 10 公里以内的所有酒店 (其中`Location`是类型的字段`Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

查找描述为多边形的给定视区中的所有酒店 (其中`Location`是 Edm.GeographyPoint 类型的字段)。 多边形必须关闭，这意味着第一个和最后一个点设置必须相同。 此外，[点必须以逆时针顺序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

查找"Description"字段为 null 的所有酒店。 该字段将为 null，如果永远不会进行设置，或如果显式设置为 null:  

    $filter=Description eq null

查找所有酒店名称等于 Sea 视图汽车旅馆或预算酒店）。 这些短语包含空格，并且空间默认分隔符。 作为第三个字符串参数，可以在单引号中指定备用分隔符：  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找所有酒店名称等于 Sea 视图汽车旅馆或预算酒店分隔 |):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找其中所有聊天室都已标记 wifi 或浸泡的所有酒店：

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

在集合中查找短语匹配项，例如标记中的“heated towel racks”或“hairdryer included”。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

查找包含“waterfront”一词的文档。 此筛选器查询与包含 `search=waterfront` 的[搜索请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)相同。

    $filter=search.ismatchscoring('waterfront')

查找带有“hostel”一词且评分大于或等于 4 分的文档，或带有“motel”一词且评分等于 5 分的文档。 此请求无法表示不带`search.ismatchscoring`函数，因为它将全文搜索与筛选器的操作使用结合`or`。

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

查找没有“luxury”一词的文档。

    $filter=not search.ismatch('luxury')

查找包含短语“ocean view”或评分等于 5 分的文档。 `search.ismatchscoring` 查询仅针对 `HotelName` 和 `Description` 字段执行。 将如此--返回酒店匹配仅析取的第二个子句的文档`Rating`等于 5。 这些文档将返回分数等于零，以使其具有清除它们不符合任何表达式的已评分的部分。

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

查找酒店，其中的术语"酒店"和"机场"，为不超过五个词相隔在说明中，以及所有聊天室都所非吸烟的位置。 此查询使用[完整 Lucene 查询语言](query-lucene-syntax.md)。

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
