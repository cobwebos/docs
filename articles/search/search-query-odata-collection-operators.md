---
title: OData 集合运算符参考-Azure 搜索
description: OData 集合运算符，所有，并在 Azure 搜索查询中的 lambda 表达式。
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079933"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>在 Azure 搜索中的 OData 集合运算符`any`和 `all`

编写时[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)若要使用与 Azure 搜索，通常很有用对集合字段进行筛选。 您可以使用实现此类情况的目的`any`和`all`运算符。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的使用的 OData 表达式语法`any`或`all`。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

有三种形式的表达式的筛选器集合。

- 前两个循环访问集合字段，将应用于集合的每个元素的 lambda 表达式形式的谓词。
  - 使用表达式`all`返回`true`如果谓词为集合中的每个元素，则返回 true。
  - 使用表达式`any`返回`true`如果谓词的集合的至少一个元素，则返回 true。
- 第三种集合形式筛选使用`any`而无需 lambda 表达式来测试是否集合字段为空。 如果集合具有的任何元素，它将返回`true`。 如果集合为空，它将返回`false`。

一个**lambda 表达式**集合在筛选器是类似的一种编程语言中的循环主体。 它定义一个变量，名为**范围变量**，包含当前在迭代期间集合的元素。 它还定义了另一个是要应用于集合的每个元素的范围变量的筛选器条件的布尔表达式。

## <a name="examples"></a>示例

匹配文档`tags`字段包含字符串"wifi"完全：

    tags/any(t: t eq 'wifi')

匹配文档的位置的每个元素`ratings`字段介于 3 和 5，非独占之间：

    ratings/all(r: r ge 3 and r le 5)

任何在地域协调中匹配文档`locations`字段是在给定的多边形内：

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

匹配文档的位置`rooms`字段为空：

    not rooms/any()

匹配的文档位置对于所有聊天室`rooms/amenities`字段包含"电视"和`rooms/baseRate`小于 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>限制

筛选器表达式不是每个功能是在 lambda 表达式的主体内可用。 限制不同，具体取决于你想要筛选的集合字段的数据类型。 下表总结了限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

有关这些限制以及示例的详细信息，请参阅[故障排除 Azure 搜索中的集合筛选器](search-query-troubleshoot-collection-filters.md)。 这些限制存在有关原因的更深入信息，请参阅[了解 Azure 搜索中的集合筛选器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
