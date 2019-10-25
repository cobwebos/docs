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
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793478"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 认知搜索中的 moreLikeThis （预览版）

> [!Note]
> moreLikeThis 是预览版，不适用于生产。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持 .NET SDK。

`moreLikeThis=[key]` 是[搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的查询参数，可查找与文档键所指定文档类似的文档。 当使用 `moreLikeThis` 生成搜索请求时，使用从最匹配的给定文档中提取的搜索项来生成查询。 然后使用生成的查询来生成搜索请求。 默认情况下，将考虑所有可搜索字段的内容，而不是使用 `searchFields` 参数指定的任何受限字段。 `moreLikeThis` 参数不能与搜索参数 `search=[string]` 一起使用。

默认情况下，将考虑所有顶级可搜索字段的内容。 如果要改为指定特定的字段，可以使用 `searchFields` 参数。 

不能对[复杂类型](search-howto-complex-data-types.md)中的可搜索子字段使用 moreLikeThis。

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

您可以使用任何 web 测试工具来试验此功能。  对于本练习，我们建议使用 Postman。

> [!div class="nextstepaction"]
> [使用 Postman 浏览 Azure 认知搜索 REST Api](search-get-started-postman.md)