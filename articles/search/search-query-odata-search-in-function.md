---
title: OData search.in 函数参考-Azure 搜索
description: 在 Azure 搜索查询中的 OData search.in 函数。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: f72a59aac448796cf15220e15a3c8a4f12803bb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079725"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` Azure 搜索中的函数

中的常见方案[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)是检查每个文档中的单个字段是否等于多个可能值之一。 例如，这是某些应用程序如何实现[安全修整](search-security-trimming-for-azure-search.md)-通过检查包含一个或多个主体 Id 与表示用户发出查询的主体 Id 的列表的字段。 一种方式编写此类查询是使用[ `eq` ](search-query-odata-comparison-operators.md)并[ `or` ](search-query-odata-logical-operators.md)运算符：

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

但是，还有一个较短方式来编写本文时，使用`search.in`函数：

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 除了更短且更易于使用的读取`search.in`还提供了[性能优势](#bkmk_performance)，并避免某些[大小限制的筛选器](search-query-odata-filter.md#bkmk_limits)时有数百或数千台值若要包括在筛选器中。 出于此原因，我们强烈建议使用`search.in`而不是更复杂析取相等表达式。

> [!NOTE]
> 最近引入的 OData 标准的版本 4.01 [ `in`运算符](http://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)，其中包含类似的行为`search.in`Azure 搜索中的函数。 但是，Azure 搜索不支持此运算符，因此必须使用`search.in`函数。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的语法`search.in`函数：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

`search.in`函数测试是否给定的字符串字段或范围变量是相等的给定值的列表。 变量和列表中的每个值之间的相等性确定区分大小写的方式进行相同的方式与对`eq`运算符。 因此，`search.in(myfield, 'a, b, c')` 等表达式相当于 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，但 `search.in` 的表现会好得多。

有两种重载的`search.in`函数：

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

下表中定义参数：

| 参数名称 | Type | 描述 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字符串字段引用 (或某个范围变量通过这种情况中的字符串集合字段位置`search.in`内部使用`any`或`all`表达式)。 |
| `valueList` | `Edm.String` | 包含要匹配的值分隔的列表的字符串`variable`参数。 如果`delimiters`未指定参数，默认分隔符为空格和逗号。 |
| `delimiters` | `Edm.String` | 一个字符串，其中每个字符将被视为一个分隔符分析时`valueList`参数。 此参数的默认值是`' ,'`这意味着会分开使用空格和/或以逗号分隔的任何值。 如果您需要使用非空格和逗号分隔符，因为你的值包括这些字符，则可以指定备用分隔符如`'|'`此参数中。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>性能 `search.in`

如果使用 `search.in`，则当第二个参数包含数百个或数千个值的列表时，可以获得次秒级响应时间。 可以将传递到项的数量没有显式限制`search.in`，尽管仍然受到最大请求大小。 但是，延迟会随着值数量的增长而增加。

## <a name="examples"></a>示例

查找所有酒店名称等于 Sea 视图汽车旅馆或预算酒店。 包含空格（默认分隔符）的短语。 作为第三个字符串参数，可以在单引号中指定备用分隔符：  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找所有酒店名称等于 Sea 视图汽车旅馆或预算酒店分隔 |):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找具有已标记 wifi 或浸泡的聊天室的所有酒店：

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

在集合中查找短语匹配项，例如标记中的“heated towel racks”或“hairdryer included”。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

查找不带标记 motel 或 cabin 的所有酒店：

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
