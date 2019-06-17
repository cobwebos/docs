---
title: OData search.score 函数参考-Azure 搜索
description: 在 Azure 搜索查询中的 OData search.score 函数。
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079686"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` Azure 搜索中的函数

如果要将查询发送到 Azure 搜索不带[ **$orderby**参数](search-query-odata-orderby.md)，返回的结果将按相关性评分排序降序排序。 甚至当您使用 **$orderby**，相关性评分将用于并列默认情况下。 但是，有时它很有用的初始排序条件，以及某些其他条件的相关性评分用作补救措施。 `search.score`功能，您可以执行此操作。

## <a name="syntax"></a>语法

**$orderby** 中 `search.score` 的语法为 `search.score()`。 `search.score` 函数不接受任何参数。 它可以用于`asc`或`desc`排序顺序说明符，就像任何其他子句中一样 **$orderby**参数。 它可以出现在列表中的排序条件的任何位置。

## <a name="example"></a>示例

排序降序排序依据的酒店`search.score`和`rating`，然后在升序按距离从给定的坐标以便之间具有相同级别的两个酒店，最近的一个首先列出的是：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
