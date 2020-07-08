---
title: moreLikeThis（预览版）查询功能
titleSuffix: Azure Cognitive Search
description: 介绍了 moreLikeThis（预览版）功能，该功能可在 Azure 认知搜索 REST API 的预览版中找到。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9fb43a0d39beacf02a6949228eaa32a719164987
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552235"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 认知搜索中的 moreLikeThis（预览版）

> [!IMPORTANT] 
> 此功能目前处于公开预览状态。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2020-06-30-Preview](search-api-preview.md)提供此功能。 目前不支持门户或 .NET SDK。

`moreLikeThis=[key]` 是[搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中的查询参数，用于查找与文档键所指定文档类似的文档。 当使用 `moreLikeThis` 生成搜索请求时，使用从最匹配的给定文档中提取的搜索项来生成查询。 然后使用生成的查询来生成搜索请求。 默认情况下，会考虑所有可搜索字段的内容，使用 `searchFields` 参数指定的限制字段除外。 `moreLikeThis` 参数不能与搜索参数 `search=[string]` 一起使用。

默认情况下，会考虑所有顶级的可搜索字段的内容。 若要改为指定特定字段，则可使用 `searchFields` 参数。 

不能在[复杂类型](search-howto-complex-data-types.md)的可搜索子字段上使用 `MoreLikeThis`。

## <a name="examples"></a>示例

以下所有示例均使用快速入门中的酒店示例[：在 Azure 门户中创建搜索索引](search-get-started-portal.md)。

### <a name="simple-query"></a>简单查询

以下查询会查找其描述字段与 `moreLikeThis` 参数指定的源文档字段最相似的文档：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

在此示例中，请求会搜索与 `HotelId` 为 29 的酒店类似的酒店。
还可以使用 HTTP POST 调用 `MoreLikeThis`，而不是使用 HTTP GET：

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>应用筛选器

`MoreLikeThis` 可以与其他常见查询参数（如 `$filter`）组合。 例如，可以将查询限制为仅返回类别为“Budget”且评级高于 3.5 的酒店：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>选择字段并限制结果

`$top` 选择器可用于限制 `MoreLikeThis` 查询中应返回的结果数。 此外，还可以使用 `$select` 选择字段。 此处选择了前三个酒店及其 ID、名称和评级： 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>后续步骤

可以使用任何 Web 测试工具来试验此功能。  建议使用 Postman 完成此练习。

> [!div class="nextstepaction"]
> [使用 Postman 探索 Azure 认知搜索 REST API](search-get-started-postman.md)
