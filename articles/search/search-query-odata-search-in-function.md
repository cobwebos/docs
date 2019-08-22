---
title: OData search.in 函数引用-Azure 搜索
description: Azure 搜索查询中的 OData search.in 函数。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 8bac0205fa2de8378abaa4d9e8ba8e05ea69192e
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647941"
---
# <a name="odata-searchin-function-in-azure-search"></a>Azure `search.in`搜索中的 OData 函数

[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)中的常见方案是检查每个文档中的单个字段是否等于多个可能值中的一个。 例如, 以下是某些应用程序实现[安全修整](search-security-trimming-for-azure-search.md)的方式: 通过检查包含一个或多个主体 id 的字段, 将其用于表示发出查询的用户的主体 id 列表。 编写类似于下面的查询的一种方法是使用[`eq`](search-query-odata-comparison-operators.md) and [`or`](search-query-odata-logical-operators.md)运算符:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

不过, 有一种更简单的方法来编写此`search.in`函数, 使用函数:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 除了更简单、更易于阅读以外, `search.in`使用还可以提高[性能](#bkmk_performance), 并避免在筛选器中包含数以百计甚至上千个值时[筛选器的某些大小限制](search-query-odata-filter.md#bkmk_limits)。 出于此原因, 我们强烈建议使用`search.in` , 而不是更复杂的相等表达式析取。

> [!NOTE]
> OData 标准的4.01 版最近引入了[ `in`运算符](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), 该运算符的行为类似于 Azure 搜索`search.in`中的函数。 但是, Azure 搜索不支持此运算符, 因此你必须`search.in`改用函数。

## <a name="syntax"></a>语法

以下 EBNF ([扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义`search.in`函数的语法:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

`search.in`函数测试给定字符串字段或范围变量是否等于给定的值列表之一。 变量与列表中每个值之间的相等性以区分大小写的方式确定, 这与`eq`运算符的方式相同。 因此，`search.in(myfield, 'a, b, c')` 等表达式相当于 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，但 `search.in` 的表现会好得多。

`search.in`函数有两个重载:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

下表中定义了这些参数:

| 参数名称 | 类型 | 描述 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字符串字段引用 (在`search.in` `any`或`all`表达式内使用的情况下, 为字符串集合字段的范围变量)。 |
| `valueList` | `Edm.String` | 一个字符串, 其中包含要与`variable`参数匹配的值的分隔列表。 如果未指定参数, 则默认分隔符为空格和逗号。 `delimiters` |
| `delimiters` | `Edm.String` | 一个字符串, 其中每个字符在分析`valueList`参数时被视为分隔符。 此参数的默认值是`' ,'`指分隔它们之间空格和/或逗号的任何值。 如果你需要使用空格和逗号以外的分隔符, 因为你的值包括这些字符, 则可以在此参数中`'|'`指定替换分隔符 (如)。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>性能`search.in`

如果使用 `search.in`，则当第二个参数包含数百个或数千个值的列表时，可以获得次秒级响应时间。 虽然你仍受最大请求大小的限制, 但你可以`search.in`传递到的项目数没有显式限制。 但是，延迟会随着值数量的增长而增加。

## <a name="examples"></a>示例

查找名称等于 "海平面 View motel" 或 "预算酒店" 的所有酒店。 包含空格（默认分隔符）的短语。 您可以使用单引号指定替代分隔符作为第三个字符串参数:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找名称等于 "海平面 View motel" 或 "预算酒店" 的所有酒店, 用 "|" 分隔:

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找带有带标记 "wifi" 或 "浴盆" 的房间的所有酒店:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

在集合中查找短语匹配项，例如标记中的“heated towel racks”或“hairdryer included”。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

查找未标记为 "motel" 或 "cabin'" 的所有酒店:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
