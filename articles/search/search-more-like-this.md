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
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721734"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 认知搜索中的 moreLikeThis （预览版）

> [!IMPORTANT] 
> 此功能目前处于公开预览状态。 提供的预览功能不带服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。

`moreLikeThis=[key]` 是[搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中的查询参数，用于查找与文档键所指定文档类似的文档。 当使用 `moreLikeThis` 生成搜索请求时，使用从最匹配的给定文档中提取的搜索项来生成查询。 然后使用生成的查询来生成搜索请求。 默认情况下，会考虑所有可搜索字段的内容，使用 `searchFields` 参数指定的限制字段除外。 `moreLikeThis` 参数不能与搜索参数 `search=[string]` 一起使用。

默认情况下，会考虑所有顶级的可搜索字段的内容。 若要改为指定特定字段，则可使用 `searchFields` 参数。 

不能在可以搜索的[复杂类型](search-howto-complex-data-types.md)的子字段上使用 moreLikeThis。

## <a name="examples"></a>示例 

下面是 moreLikeThis 查询的一个示例。 该查询会查找其描述字段与 `moreLikeThis` 参数指定的源文档字段最相似的文档。

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>后续步骤

可以使用任何 Web 测试工具来试验此功能。  建议使用 Postman 完成此练习。

> [!div class="nextstepaction"]
> [使用 Postman 浏览 Azure 认知搜索 REST Api](search-get-started-postman.md)