---
title: OData 语言概述
titleSuffix: Azure Cognitive Search
description: 适用于 Azure 认知搜索查询的筛选器、选择和排序的 OData 语言概述。
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
ms.openlocfilehash: e0db41098287ff011416932a0d44a1cb9f76127d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786164"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>适用于 Azure 中的 `$filter`、`$orderby`和 `$select` 的 OData 语言概述认知搜索

Azure 认知搜索支持 **$filter**、 **$orderby**和 **$select**表达式的 OData 表达式语法的子集。 筛选表达式在查询分析期间进行求值，将搜索范围限制为特定字段或添加索引扫描期间使用的匹配条件。 顺序表达式作为后处理步骤应用于结果集，以对返回的文档进行排序。 选择表达式确定要包含在结果集中的文档字段。 这些表达式的语法与**search**参数中使用的[简单](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查询语法不同，但在引用字段的语法中有一些重叠。

本文概述了筛选器中使用的 OData 表达式语言、排序依据和选择表达式。 此语言以 "自下而上" 显示，从最基本的元素开始，然后在这些元素上构建。 在单独的文章中介绍了每个参数的顶级语法：

- [$filter 语法](search-query-odata-filter.md)
- [$orderby 语法](search-query-odata-orderby.md)
- [$select 语法](search-query-odata-select.md)

OData 表达式的范围从 simple 到非常复杂，但它们都共享公共元素。 Azure 认知搜索中的 OData 表达式的最基本部分如下：

- **字段路径**，引用索引的特定字段。
- **常量**，是特定数据类型的文本值。

> [!NOTE]
> Azure 认知搜索中的术语在某些方面与[OData 标准](https://www.odata.org/documentation/)不同。 我们在 Azure 中调用一个**字段**，认知搜索在 OData 中称为**属性**，与**字段路径**和**属性路径**类似。 包含 Azure 认知搜索中**文档**的**索引**在 OData 中更普遍称为包含**实体**的**实体集**。 Azure 认知搜索术语贯穿整个参考。

## <a name="field-paths"></a>字段路径

以下 EBNF （[扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义字段路径的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

字段路径由一个或多个用斜杠分隔的**标识符**组成。 每个标识符都是一系列必须以 ASCII 字母或下划线开头的字符，并且只能包含 ASCII 字母、数字或下划线。 字母可以是大写或小写字母。

标识符可以引用字段的名称，也可以引用筛选器中[集合表达式](search-query-odata-collection-operators.md)（`any` 或 `all`）的上下文中的**范围变量**。 范围变量类似于循环变量，它表示集合的当前元素。 对于复杂的集合，该变量表示对象，这就是为什么可以使用字段路径来引用变量的子字段。 这类似于许多编程语言中的点表示法。

下表显示了字段路径的示例：

| 字段路径 | 描述 |
| --- | --- |
| `HotelName` | 引用索引的顶级字段 |
| `Address/City` | 引用索引中复杂字段的 `City` 子字段;在此示例中，`Address` 的类型 `Edm.ComplexType` |
| `Rooms/Type` | 引用索引中复杂集合字段的 `Type` 子字段;在此示例中，`Rooms` 的类型 `Collection(Edm.ComplexType)` |
| `Stores/Address/Country` | 引用索引中复杂集合字段的 `Address` 子字段的 `Country` 子字段;`Stores` 属于 `Collection(Edm.ComplexType)` 类型，`Address` 为此示例中的类型 `Edm.ComplexType` |
| `room/Type` | 引用 `room` 范围变量的 `Type` 子字段，例如在筛选器表达式中 `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 引用 `store` 范围变量的 `Address` 子字段的 `Country` 子字段，例如在筛选器表达式中 `Stores/any(store: store/Address/Country eq 'Canada')` |

字段路径的含义因上下文而异。 在筛选器中，字段路径引用当前文档中某个字段的*单个实例*的值。 在其他上下文中，如 **$orderby**、 **$select**或 in[现场 search in full Lucene 语法](query-lucene-syntax.md#bkmk_fields)，字段路径引用字段本身。 在筛选器中使用字段路径时，这种差异会产生一些后果。

请考虑字段路径 `Address/City`。 在筛选器中，这是指当前文档的单个城市，如 "旧金山"。 相反，`Rooms/Type` 是指许多房间的 `Type` 子字段（例如，第一个房间的 "标准"、第二个房间的 "高级" 等）。 由于 `Rooms/Type` 不引用子字段 `Type`的*单个实例*，因此不能直接在筛选器中使用。 相反，若要筛选房间类型，可以将[lambda 表达式](search-query-odata-collection-operators.md)用于范围变量，如下所示：

    Rooms/any(room: room/Type eq 'deluxe')

在此示例中，范围变量 `room` 显示在 `room/Type` 字段路径中。 这样，`room/Type` 指当前文档中当前聊天室的类型。 这是 `Type` 子字段的单个实例，因此可直接在筛选器中使用。

### <a name="using-field-paths"></a>使用字段路径

在[Azure 认知搜索 REST api](https://docs.microsoft.com/rest/api/searchservice/)的许多参数中使用字段路径。 下表列出了可以使用的所有位置，以及对使用情况的任何限制：

| API | 参数名称 | 限制 |
| --- | --- | --- |
| [创建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `suggesters/sourceFields` | None |
| [创建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/text/weights` | 只能引用可**搜索**字段 |
| [创建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/functions/fieldName` | 只能引用可**筛选**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 当 `queryType` `full` 时，`search` | 只能引用可**搜索**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | 只能引用**可查找**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | 只能引用可**搜索**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | 只能引用可**搜索**字段 |
| [建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | 只能引用属于[建议器](index-add-suggesters.md)的字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)、[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | 只能引用可**筛选**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)和[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | 只能引用可**排序**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)、[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[查找](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | 只能引用可**检索**字段 |

## <a name="constants"></a>称作

OData 中的常量是给定[实体数据模型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)（EDM）类型的文字值。 请参阅[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)，了解 Azure 认知搜索中支持的类型列表。 不支持集合类型的常量。

下表显示了 Azure 认知搜索支持的各种数据类型的常量示例：

| 数据类型 | 示例常量 |
| --- | --- |
| `Edm.Boolean` | `true`，`false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`、`-1.2e7`、`NaN`、`INF`、`-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`，`-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

以下 EBNF （[扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义上表中所示的大部分常量的语法。 可以在[Azure 认知搜索的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)中找到地理空间类型的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

## <a name="building-expressions-from-field-paths-and-constants"></a>从字段路径和常量生成表达式

字段路径和常量是 OData 表达式中最基本的部分，但它们已经是完整的表达式。 事实上，Azure 认知搜索中的 **$select**参数不是任何内容，而是以逗号分隔的字段路径列表， **$orderby**并不比 **$select**复杂得多。 如果您在索引中有一个类型为 "`Edm.Boolean`" 的字段，甚至可以编写一个筛选器，该筛选器只是该字段的路径。 常数 `true` 和 `false` 同样是有效的筛选器。

但是，大多数情况下，您需要多个引用多个字段和常量的复杂表达式。 这些表达式的生成方式不同，具体取决于参数。

以下 EBNF （[扩展巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义 **$filter**、 **$orderby**和 **$select**参数的语法。 这些是从引用字段路径和常量的更简单的表达式生成的：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

**$Orderby**和 **$select**参数都是以逗号分隔的表达式列表。 **$Filter**参数是由简单的子表达式组成的布尔表达式。 这些子表达式使用逻辑运算符（例如[`and`、`or`和 `not`](search-query-odata-logical-operators.md)、比较运算符（如[`eq`、`lt`、`gt`等](search-query-odata-comparison-operators.md)）和集合运算符（如[`any` 和 @no__t_10）组合在一起。_ ](search-query-odata-collection-operators.md).

以下文章更详细地探讨了 **$filter**、 **$orderby**和 **$select**参数：

- [Azure 中的 OData $filter 语法认知搜索](search-query-odata-filter.md)
- [Azure 中的 OData $orderby 语法认知搜索](search-query-odata-orderby.md)
- [Azure 中的 OData $select 语法认知搜索](search-query-odata-select.md)

## <a name="see-also"></a>另请参阅  

- [Azure 认知搜索中的分面导航](search-faceted-navigation.md)
- [Azure 认知搜索中的筛选器](search-filters.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查询语法](query-lucene-syntax.md)
- [Azure 认知搜索中的简单查询语法](query-simple-syntax.md)
