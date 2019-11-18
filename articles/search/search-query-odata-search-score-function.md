---
title: OData 搜索计分函数引用
titleSuffix: Azure Cognitive Search
description: 使用 Azure 中的搜索计分函数的语法和参考文档认知搜索查询。
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113137"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure 中的 OData `search.score` 函数认知搜索

将查询发送到 Azure 认知搜索没有[ **$orderby**参数](search-query-odata-orderby.md)时，返回的结果将按相关性分数降序排序。 即使你确实使用 **$orderby**，默认情况下，相关性评分也将用于打破平分的局面。 但是，有时使用相关性评分作为初始排序条件，并使用其他一些条件作为决定因素是有用的。 `search.score` 函数允许你执行此操作。

## <a name="syntax"></a>语法

`search.score`$orderby**中** 的语法为 `search.score()`。 `search.score` 函数不接受任何参数。 它可以与 `asc` 或 `desc` 排序顺序说明符一起使用，就像 **$orderby** 参数中的任何其他子句一样。 它可以出现在排序条件列表中的任何位置。

## <a name="example"></a>示例

按 `search.score` 和 `rating` 对酒店进行降序排序，然后按距给定坐标的距离进行升序排序，以便在两个具有相同评分的酒店中，将距离最近的酒店列在前面：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;AZURE 认知搜索 EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
