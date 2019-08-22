---
title: OData 搜索。计分函数引用-Azure 搜索
description: Azure 搜索查询中的 OData 搜索功能。
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647519"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Azure `search.score`搜索中的 OData 函数

如果在没有[ **$orderby**参数](search-query-odata-orderby.md)的情况下将查询发送到 Azure 搜索, 则返回的结果将按相关性分数降序排序。 即使使用 **$orderby**, 默认情况下也会使用关联分数来中断关联。 但是, 有时将相关性分数用作初始排序标准, 并使用一些其他条件作为附加断路器会很有用。 `search.score`函数允许执行此操作。

## <a name="syntax"></a>语法

**$orderby** 中 `search.score` 的语法为 `search.score()`。 `search.score` 函数不接受任何参数。 它可以与`asc` or `desc`排序说明符一起使用, 就像 **$orderby**参数中的任何其他子句一样。 它可以出现在排序条件列表中的任何位置。

## <a name="example"></a>示例

按`search.score` 和`rating`, 然后按降序对酒店进行排序, 然后按与给定坐标的距离进行升序排序, 以便在两个具有相同分级的酒店之间进行排序, 最接近的一家酒店首先列出:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
