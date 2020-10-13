---
title: OData search.score 函数参考
titleSuffix: Azure Cognitive Search
description: 语法和参考文档，用于在 Azure 认知搜索查询中使用 search.score 函数。
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
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923970"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure 认知搜索中的 OData `search.score` 函数

在不使用 [ **$orderby** 参数](search-query-odata-orderby.md)的情况下向 Azure 认知搜索发送查询时，返回的结果将按相关性评分降序排序。 即使你确实使用 **$orderby**，默认情况下，相关性评分也将用于打破平分的局面。 但是，有时使用相关性评分作为初始排序条件，并使用其他一些条件作为决定因素是有用的。 `search.score` 函数允许你执行此操作。

## <a name="syntax"></a>语法

**$orderby** 中 `search.score` 的语法为 `search.score()`。 `search.score` 函数不接受任何参数。 它可以与 `asc` 或 `desc` 排序顺序说明符一起使用，就像 **$orderby** 参数中的任何其他子句一样。 它可以出现在排序条件列表中的任何位置。

## <a name="example"></a>示例

按 `search.score` 和 `rating` 对酒店进行降序排序，然后按距给定坐标的距离进行升序排序，以便在两个具有相同评分的酒店中，将距离最近的酒店列在前面：

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 认知搜索 REST API）](/rest/api/searchservice/Search-Documents)