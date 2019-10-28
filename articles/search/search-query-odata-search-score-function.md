---
title: OData 搜索计分函数引用
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索查询中的 OData 搜索功能。
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
ms.openlocfilehash: 500ac4f3a44d54e367ddc4ee5efc9514d603cab6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793276"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure 中的 OData `search.score` 函数认知搜索

将查询发送到 Azure 认知搜索没有[ **$orderby**参数](search-query-odata-orderby.md)时，返回的结果将按相关性分数降序排序。 即使使用 **$orderby**，默认情况下也会使用关联分数来中断关联。 但是，有时将相关性分数用作初始排序标准，并使用一些其他条件作为附加断路器会很有用。 `search.score` 函数允许执行此操作。

## <a name="syntax"></a>语法

**$orderby** 中 `search.score` 的语法为 `search.score()`。 `search.score` 函数不接受任何参数。 它可与 `asc` 或 `desc` 排序顺序说明符一起使用，就像 **$orderby**参数中的任何其他子句一样。 它可以出现在排序条件列表中的任何位置。

## <a name="example"></a>示例

按降序对酒店排序，`search.score` 和 `rating`，然后按从给定坐标开始的升序进行排序，以便在两个具有相同分级的酒店之间进行排序，最接近的一家酒店首先列出：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;AZURE 认知搜索 EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
