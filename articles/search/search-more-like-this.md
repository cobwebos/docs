---
title: Azure 搜索中的 moreLikeThis（预览版）- Azure 搜索
description: Azure 搜索 REST API 中公开了有关 moreLikeThis 功能（预览）功能的初步文档。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 5723f1ab7258a9e0d672b5c0fd9fd0b9c4dc8721
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522922"
---
# <a name="morelikethis-in-azure-search"></a>Azure 搜索中的 moreLikeThis

> [!Note]
> moreLikeThis 处于预览状态，不用于生产环境中使用。 [REST API 版本 2019年-05-06-预览](search-api-preview.md)提供此功能。 没有目前.NET SDK 支持。

`moreLikeThis=[key]` 是中的查询参数[搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) ，查找文档相似的文档键所指定的文档。 当使用 `moreLikeThis` 生成搜索请求时，使用从最匹配的给定文档中提取的搜索项来生成查询。 然后使用生成的查询来生成搜索请求。 默认情况下，所有可搜索字段的内容被视为，使用指定的任何限制字段减去`searchFields`参数。 `moreLikeThis` 参数不能与搜索参数 `search=[string]` 一起使用。

默认情况下，被视为所有的顶级可搜索字段的内容。 如果你想要改为指定特定字段，则可以使用`searchFields`参数。 

不能使用可搜索子字段中的 moreLikeThis[复杂类型](search-howto-complex-data-types.md)。

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

可以使用任何 web 测试工具来体验此功能。  我们建议对于此练习使用 Postman。

> [!div class="nextstepaction"]
> [探索 Azure 搜索 REST Api 使用 Postman](search-fiddler.md)