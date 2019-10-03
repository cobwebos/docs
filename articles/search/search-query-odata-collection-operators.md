---
title: OData 集合运算符引用-Azure 搜索
description: Azure 搜索查询中的 OData 集合运算符、any 和 all 以及 lambda 表达式。
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647631"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Azure 搜索中的 OData 集合运算符`any` -和`all`

编写要用于 Azure 搜索的[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)时, 对集合字段进行筛选通常很有用。 您可以使用`any`和`all`运算符来实现此目的。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义使用`any`或`all`的 OData 表达式的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

有三种形式的表达式可用于筛选集合。

- 前两个循环访问集合字段, 将 lambda 表达式形式的谓词应用于集合的每个元素。
  - 如果集合的`all`每`true`个元素都为 true, 则使用的表达式将返回。
  - 如果谓词对于`any`集合`true`的至少一个元素为 true, 则使用的表达式将返回。
- 第三种形式的集合筛选`any`器不使用 lambda 表达式来测试集合字段是否为空。 如果集合中有任何元素, 则返回`true`。 如果集合为空, 则返回`false`。

集合筛选器中的**lambda 表达式**类似于编程语言中的循环体。 它定义了一个名为**范围变量**的变量, 该变量在迭代期间保存集合的当前元素。 它还定义了另一个布尔表达式, 该表达式是要应用于集合的每个元素的范围变量的筛选条件。

## <a name="examples"></a>示例

匹配其`tags`字段只包含字符串 "wifi" 的文档:

    tags/any(t: t eq 'wifi')

匹配该`ratings`字段的每个元素介于3和 5 (含) 之间的文档:

    ratings/all(r: r ge 3 and r le 5)

匹配`locations`字段中任意地理坐标位于给定多边形内的文档:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

匹配`rooms`字段为空的文档:

    not rooms/any()

匹配所有房间的文档, `rooms/amenities`字段包含 "电视`rooms/baseRate` ", 小于 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>限制

并非每个筛选器表达式功能都可在 lambda 表达式的主体中使用。 此限制因你要筛选的集合字段的数据类型而异。 下表总结了这些限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

有关这些限制以及示例的更多详细信息, 请参阅[Azure 搜索中的集合筛选器故障排除](search-query-troubleshoot-collection-filters.md)。 有关为何存在这些限制的详细信息, 请参阅[了解 Azure 搜索中的集合筛选器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
