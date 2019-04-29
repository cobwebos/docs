---
title: Azure 搜索中的 moreLikeThis（预览版）- Azure 搜索
description: Azure 搜索 REST API 中公开了有关 moreLikeThis 功能（预览）功能的初步文档。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283322"
---
# <a name="morelikethis-in-azure-search-preview"></a>Azure 搜索中的 moreLikeThis（预览版）

`moreLikeThis=[key]` 是[搜索 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中的查询参数。 通过在搜索查询中指定 `moreLikeThis` 参数，可以查找与文档键所指定文档类似的文档。 当使用 `moreLikeThis` 生成搜索请求时，使用从最匹配的给定文档中提取的搜索项来生成查询。 然后使用生成的查询来生成搜索请求。 默认情况下，考虑所有 `searchable` 字段的内容，除非 `searchFields` 参数用于限制字段。 `moreLikeThis` 参数不能与搜索参数 `search=[string]` 一起使用。

## <a name="examples"></a>示例 

下面是 moreLikeThis 查询的一个示例。 该查询会查找其描述字段与 `moreLikeThis` 参数指定的源文档字段最相似的文档。

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>功能可用性

moreLikeThis 功能目前处于预览状态，并且仅支持预览版 api-version、`2015-02-28-Preview` 和 `2016-09-01-Preview`。 由于 API 版本按请求来指定，因此它的正式版 (GA) 和预览版 API 可能会在同一应用上合并。 但是，预览版 API 不在 SLA 下且功能可能会更改，因此不建议在生产应用程序中进行使用。
