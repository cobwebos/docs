---
title: OData 表达式语法参考
titleSuffix: Azure Cognitive Search
description: Azure 中的 OData 表达式的正式语法和语法规范认知搜索查询。
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793234"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>适用于 Azure 认知搜索的 OData 表达式语法参考

Azure 认知搜索在整个 API 中使用[OData 表达式](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html)作为参数。 最常见的情况是，OData 表达式用于 `$orderby` 和 `$filter` 参数。 这些表达式可能很复杂，其中包含多个子句、函数和运算符。 但是，甚至简单的 OData 表达式（如属性路径）都在 Azure 认知搜索 REST API 的许多部分中使用。 例如，路径表达式用于引用 API 中任何位置的复杂字段的子字段，例如，在[建议器](index-add-suggesters.md)中列出子字段、[计分函数](index-add-scoring-profiles.md)、`$select` 参数，甚至是[在 Lucene 查询中现场搜索](query-lucene-syntax.md).

本文介绍使用正式语法的所有这些形式的 OData 表达式。 此外，还提供了一个[交互式关系图](#syntax-diagram)来帮助你直观地了解语法。

## <a name="formal-grammar"></a>正式语法

我们可以使用 EBNF （[扩展巴科斯-诺尔范式形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）语法描述 Azure 认知搜索支持的 OData 语言的子集。 规则按 "自上而下" 列出，从最复杂的表达式开始，然后细分为更多的基元表达式。 顶部是对应于 Azure 认知搜索 REST API 的特定参数的语法规则：

- [`$filter`](search-query-odata-filter.md)，由 `filter_expression` 规则定义。
- [`$orderby`](search-query-odata-orderby.md)，由 `order_by_expression` 规则定义。
- [`$select`](search-query-odata-select.md)，由 `select_expression` 规则定义。
- 字段路径，由 `field_path` 规则定义。 在整个 API 中使用字段路径。 它们可以引用索引的顶级字段或具有一个或多个[复杂字段](search-howto-complex-data-types.md)上级的子字段。

EBNF 是一个可浏览的[语法关系图](https://en.wikipedia.org/wiki/Syntax_diagram)，可用于以交互方式浏览语法以及其规则之间的关系。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>语法关系图

若要直观地了解 Azure 认知搜索支持的 OData 语言语法，请尝试交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>另请参阅  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查询语法](query-lucene-syntax.md)
- [Azure 认知搜索中的简单查询语法](query-simple-syntax.md)
