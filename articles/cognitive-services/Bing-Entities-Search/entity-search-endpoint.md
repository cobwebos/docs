---
title: 必应实体搜索终结点
titlesuffix: Azure Cognitive Services
description: 实体搜索 API 终结点摘要。
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: b1b89cbacf43df544261847d4e3fae396a52f423
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816270"
---
# <a name="entity-search-endpoints"></a>实体搜索终结点
实体搜索 API 包括一个终结点。

## <a name="endpoint"></a>终结点
要请求实体搜索结果，请将请求发送到以下终结点。 使用标头和 URL 参数来定义更多规范。

终结点 `GET`： 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

需要以下 URL 参数：
- mkt。 产生结果的市场。 
- q。 实体搜索查询。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应实体搜索快速入门](quickstarts/csharp.md)

## <a name="see-also"></a>另请参阅 

[必应实体搜索概述](search-the-web.md )
[API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
