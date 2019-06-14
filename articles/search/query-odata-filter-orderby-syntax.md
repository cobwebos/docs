---
title: OData 语言概述-Azure 搜索
description: OData 筛选器，语言概述选择，并为 Azure 搜索查询的排序依据。
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063708"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>有关 OData 语言概述`$filter`， `$orderby`，和`$select`Azure 搜索中

Azure 搜索支持的 OData 表达式语法子集 **$filter**， **$orderby**，并 **$select**表达式。 筛选表达式在查询分析期间进行求值，将搜索范围限制为特定字段或添加索引扫描期间使用的匹配条件。 Order by 表达式的结果集进行排序返回的文档将对应用作为后续处理步骤。 Select 表达式确定结果集中包含的文档字段。 这些表达式的语法是截然不同[简单](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查询中使用的语法**搜索**参数，尽管某些重叠的语法的情况引用字段。

本文概述了筛选器、 order by 和 select 表达式中使用的 OData 表达式语言。 "自下而上"、 开头的最基本元素和在其上构建，会显示语言。 在单独的文章中介绍的每个参数的顶级语法：

- [$filter 语法](search-query-odata-filter.md)
- [$orderby syntax](search-query-odata-orderby.md)
- [$select syntax](search-query-odata-select.md)

OData 表达式范围从简单到非常复杂，但它们全部共享通用元素。 Azure 搜索中的 OData 表达式的最基本部分是：

- **字段路径**，该引用特定字段的索引。
- **常量**，这都是特定数据类型的文本值。

> [!NOTE]
> Azure 搜索中的术语不同于[OData 标准](https://www.odata.org/documentation/)通过多种方式。 我们所说**字段**在 Azure 搜索中称为**属性**对于 OData 中，也同样如此**字段路径**与**属性路径**。 **索引**包含**文档**Azure 搜索中更普遍的情况中引用作为 OData**实体集**包含**实体**。 本参考文档使用 Azure 搜索术语。

## <a name="field-paths"></a>字段路径

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的字段路径的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

字段路径由一个或多个组成**标识符**由斜杠分隔。 每个标识符是必须以 ASCII 字母或下划线开头并包含 ASCII 字母、 数字或下划线的字符序列。 上限-或小写，可以是字母。

标识符可以引用到名称的一个字段，或对**范围变量**的上下文中[集合表达式](search-query-odata-collection-operators.md)(`any`或`all`) 筛选器中。 范围变量就像表示集合的当前元素的循环变量。 对于复杂集合，该变量表示一个对象，它是使用字段路径来引用该变量的子字段的原因。 这是类似于许多编程语言中的点表示法。

字段路径的示例表所示：

| 字段路径 | 描述 |
| --- | --- |
| `HotelName` | 引用索引的顶级字段 |
| `Address/City` | 是指`City`子字段的索引; 中的复杂字段`Address`属于类型`Edm.ComplexType`在此示例 |
| `Rooms/Type` | 是指`Type`子字段的索引; 中的复杂集合字段`Rooms`属于类型`Collection(Edm.ComplexType)`在此示例 |
| `Stores/Address/Country` | 是指`Country`的子字段`Address`子字段的索引; 中的复杂集合字段`Stores`属于类型`Collection(Edm.ComplexType)`并`Address`的类型`Edm.ComplexType`在此示例 |
| `room/Type` | 是指`Type`的子字段`room`范围变量，例如在筛选器表达式 `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 是指`Country`的子字段`Address`的子字段`store`范围变量，例如在筛选器表达式 `Stores/any(store: store/Address/Country eq 'Canada')` |

字段路径的含义取决于上下文。 在筛选器字段路径引用的值*单个实例*当前文档中的字段。 在其他上下文中，如 **$orderby**， **$select**，或在[完整 Lucene 语法中的现场的搜索](query-lucene-syntax.md#bkmk_fields)，字段的路径引用了字段本身。 这种差异会带来一些有关如何在筛选器中使用字段路径的后果。

字段路径，请考虑`Address/City`。 在筛选器中，这表示当前文档，例如"旧金山"的单个市/县。 与此相反，`Rooms/Type`是指`Type`子字段 （如"标准"第一个聊天室，"高级"的第二个房间，依此类推） 的许多房间。 由于`Rooms/Type`不是指*单个实例*的子字段`Type`，它不能在筛选器中直接使用。 相反，若要筛选的空间类型上，将使用[lambda 表达式](search-query-odata-collection-operators.md)通过范围变量，如下：

    Rooms/any(room: room/Type eq 'deluxe')

在此示例中，范围变量`room`将出现在`room/Type`字段路径。 这样一来，`room/Type`所引用的当前聊天室当前文档中的类型。 这是一份`Type`子字段中，因此可以在筛选器中直接使用它。

### <a name="using-field-paths"></a>使用字段路径

中的多个参数使用字段路径[Azure 搜索 API](https://docs.microsoft.com/rest/api/searchservice/)。 下表列出了在其中可以使用它们的所有位置加上对其使用情况的任何限制：

| API | 参数名称 | 限制 |
| --- | --- | --- |
| [创建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `suggesters/sourceFields` | 无 |
| [创建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/text/weights` | 只能引用**可搜索**字段 |
| [创建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/functions/fieldName` | 只能引用**可筛选**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` 当`queryType`是 `full` | 只能引用**可搜索**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | 只能引用**可查找**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | 只能引用**可搜索**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | 只能引用**可搜索**字段 |
| [建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[记忆式键入功能](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | 可以仅引用的字段属于[建议器](index-add-suggesters.md) |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)，[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)，和[记忆式键入功能](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | 只能引用**可筛选**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)和[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | 只能引用**可排序**字段 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)，[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)，和[查找](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | 只能引用**可检索**字段 |

## <a name="constants"></a>常量

在 OData 中的常量都是文本值的给定[实体数据模型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)(EDM) 类型。 请参阅[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)有关 Azure 搜索支持的类型的列表。 不支持集合类型的常量。

下表显示了为每个 Azure 搜索支持的数据类型的常量的示例：

| 数据类型 | 示例常量 |
| --- | --- |
| `Edm.Boolean` | `true`， `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`， `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的大部分如上表中所示的常量的语法。 地理空间类型的语法可以中找到[Azure 搜索中的 OData 地理空间函数](search-query-odata-geo-spatial-functions.md)。

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

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

## <a name="building-expressions-from-field-paths-and-constants"></a>字段路径和常量从生成表达式

字段路径和常量的最基本的一部分的 OData 表达式，但它们已完整表达式本身。 事实上， **$select**在 Azure 搜索中的参数只是以逗号分隔列表的字段路径，并 **$orderby**并不比复杂得多 **$select**。 如果恰好有一个类型的字段`Edm.Boolean`在索引中，您甚至可以编写只是该字段的路径筛选器。 常量`true`和`false`同样是有效的筛选器。

但是，大多数情况下将需要更复杂的表达式引用多个字段和常量。 这些表达式是内置的不同的方式，具体取决于该参数。

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的语法 **$filter**， **$orderby**，以及 **$select**参数。 这些是从构建更简单指字段路径和常量的表达式：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

**$Orderby**并 **$select**参数是更简单的表达式的这两个以逗号分隔列表。 **$Filter**参数是一个布尔表达式，其中包含的更简单的子表达式。 如使用逻辑运算符组合这些子表达式[ `and`， `or`，和`not` ](search-query-odata-logical-operators.md)，如比较运算符[ `eq`， `lt`， `gt`，依次类推](search-query-odata-comparison-operators.md)，并等集合运算符[`any`并`all` ](search-query-odata-collection-operators.md)。

**$Filter**， **$orderby**，并 **$select**以下文章中更详细地探讨了参数：

- [Azure 搜索中的 OData $filter 语法](search-query-odata-filter.md)
- [Azure 搜索中的 OData $orderby 语法](search-query-odata-orderby.md)
- [Azure 搜索中的 OData $select 语法](search-query-odata-select.md)

## <a name="see-also"></a>另请参阅  

- [Azure 搜索中的分面导航](search-faceted-navigation.md)
- [Azure 搜索中的筛选器](search-filters.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查询语法](query-lucene-syntax.md)
- [Azure 搜索中的简单查询语法](query-simple-syntax.md)
