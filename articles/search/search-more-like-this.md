---
title: moreLikeThis （预览）查询功能
titleSuffix: Azure Cognitive Search
description: 介绍了 moreLikeThis （预览版）功能，该功能可在 Azure 认知搜索的预览版本 REST API 中找到。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873805"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 认知搜索中的 moreLikeThis （预览版）

> [!IMPORTANT] 
> 此功能目前处于公开预览状态。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。

`moreLikeThis=[key]` 是[搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的查询参数，可查找与文档键所指定文档类似的文档。 当使用 `moreLikeThis` 生成搜索请求时，使用从最匹配的给定文档中提取的搜索项来生成查询。 然后使用生成的查询来生成搜索请求。 默认情况下，将考虑所有可搜索字段的内容，而不是使用 `searchFields` 参数指定的任何受限字段。 `moreLikeThis` 参数不能与搜索参数 `search=[string]` 一起使用。

默认情况下，将考虑所有顶级可搜索字段的内容。 如果要改为指定特定的字段，可以使用 `searchFields` 参数。 

不能对[复杂类型](search-howto-complex-data-types.md)中的可搜索子字段使用 `MoreLikeThis`。

## <a name="examples"></a>示例

以下所有示例均使用快速入门中的酒店示例[：在 Azure 门户中创建搜索索引](search-get-started-portal.md)。

### <a name="simple-query"></a>简单查询

下面的查询将查找其描述字段与 `moreLikeThis` 参数所指定的源文档的字段最相似的文档：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

在此示例中，请求会搜索与 `HotelId` 29 类似的酒店。
你还可以使用 HTTP POST 调用 `MoreLikeThis`，而不是使用 HTTP GET：

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>应用筛选器

`MoreLikeThis` 可以与其他常见查询参数组合，如 `$filter`。 例如，可以将查询限制为仅限其类别为 "预算" 且分级高于3.5 的酒店：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>选择字段并限制结果

`$top` 选择器可用于限制 `MoreLikeThis` 查询中应返回的结果数。 此外，可以 `$select`选择字段。 此处选择了前三个酒店及其 ID、名称和等级： 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>后续步骤

您可以使用任何 web 测试工具来试验此功能。  对于本练习，我们建议使用 Postman。

> [!div class="nextstepaction"]
> [使用 Postman 浏览 Azure 认知搜索 REST Api](search-get-started-postman.md)
