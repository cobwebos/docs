---
title: OData search.in 函数引用
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索查询中使用 search.in 函数的语法和参考文档。
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113118"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Azure 中的 OData `search.in` 函数认知搜索

在 [OData 筛选器表达式](query-odata-filter-orderby-syntax.md)中，一种常见情景是检查每个文档中的单个字段是否等于许多可能值中的一个。 例如，某些应用程序会这样进行[安全修整](search-security-trimming-for-azure-search.md)：根据一个包含主 ID（代表发出查询的用户）的列表，检查字段是否包含一个或多个主 ID。 若要编写这样的查询，一种方法是使用 [`eq`](search-query-odata-comparison-operators.md) 和 [`or`](search-query-odata-logical-operators.md) 运算符：

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

不过，有一种更简便的编写方法，这就是使用 `search.in` 函数：

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 除了更简便且更易读，使用 `search.in` 还具有[性能优势](#bkmk_performance)，并且在需要在筛选器中包含数百甚至数千个值的情况下，可以避免某些[筛选器大小限制](search-query-odata-filter.md#bkmk_limits)。 因此，我们强烈建议使用 `search.in`，而不要使用更复杂的相等表达式析取。

> [!NOTE]
> OData 标准4.01 版最近引入了[`in` 运算符](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)，该运算符的行为与 Azure 认知搜索中 `search.in` 函数的行为类似。 但是，Azure 认知搜索不支持此运算符，因此你必须改用 `search.in` 函数。

## <a name="syntax"></a>语法

以下 EBNF（[扩展巴科斯-瑙尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义 `search.in` 函数的语法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

`search.in` 函数测试给定字符串字段或范围变量是否等于给定的值列表之一。 变量与列表中每个值之间的相等性以区分大小写的方式进行确定，这与 `eq` 运算符的方式相同。 因此，`search.in(myfield, 'a, b, c')` 等表达式相当于 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，但 `search.in` 的表现会好得多。

`search.in` 函数有两个重载：

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

下表定义了这些参数：

| 参数名称 | 类型 | 说明 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字符串字段引用（在 `search.in` 或 `any` 表达式中使用 `all` 的情况下，则为基于字符串集合字段的范围变量）。 |
| `valueList` | `Edm.String` | 一个字符串，其中包含的分隔列表中的值需要与 `variable` 参数匹配。 如果未指定 `delimiters` 参数，则默认的分隔符为空格和逗号。 |
| `delimiters` | `Edm.String` | 一个字符串，其中的每个字符在分析 `valueList` 参数时会被视为分隔符。 此参数的默认值为 `' ,'`，这意味着，系统会将其中包含空格和/或逗号的任何值分开。 如果因为值包含空格和逗号而需要使用这些字符以外的分隔符，可以在此参数中指定替代分隔符，例如 `'|'`。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>`search.in` 的性能

如果使用 `search.in`，则当第二个参数包含数百个或数千个值的列表时，可以获得次秒级响应时间。 尽管仍然受到最大请求大小的限制，但可以传递给 `search.in` 的项数没有明确限制。 但是，延迟会随着值数量的增长而增加。

## <a name="examples"></a>示例

查找名称为“Sea View motel”或“Budget hotel”的所有酒店。 包含空格（默认分隔符）的短语。 可以将单引号中的备用分隔符指定为第三个字符串参数：  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找名称为“Sea View motel”或“Budget hotel”并以“|”分隔的所有酒店：

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找其房间带有“wifi”或“tub”标签的所有酒店：

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

在集合中查找短语匹配项，例如标记中的“heated towel racks”或“hairdryer included”。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

查找没有“motel”或“cabin”标签的所有酒店：

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
