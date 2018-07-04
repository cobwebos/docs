---
title: 使用排名显示答案 |Microsoft 文档
description: 演示如何使用排名显示必应实体搜索 API 返回的答案。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059985"
---
# <a name="using-ranking-to-display-results"></a>使用排名显示结果  

每个实体搜索响应包括一个 [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) 答案，与必应 Web 搜索响应中的类似，它指定搜索结果的显示方式。 排名响应会将结果分组到两极、主线和边栏内容。 两极结果是最重要或突出的结果，应首先显示。 如果不以传统主线和边栏格式显示剩余结果，必须以比边栏内容更高的可见度提供主线内容。 
  
在每个组中，[项](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items)数组标识了内容必须显示的顺序。 每个项提供两种方法来标识答案中的结果。  
  
-   `answerType` 和 `resultIndex` - `answerType` 字段标识答案（实体或位置），`resultIndex` 标识答案中的结果（例如某个实体）。 索引从零开始。  
  
-   `value` - `value` 字段包含一个 ID，它与答案或答案中结果的 ID 匹配。 答案或结果包含 ID，而不是同时包含。  
  
使用 ID 必须使排名 ID 与答案或其结果之一的 ID 匹配。 如果答案对象包含 `id` 字段，则同时显示答案的结果。 例如，如果 `Entities` 对象包含 `id` 字段，则同时显示所有实体项目。 如果 `Entities` 对象不包含 `id` 字段，则每个实体包含 `id` 字段，排名响应将实体与位置结果混合。  
  
使用 `answerType` 和 `resultIndex` 的过程分为两步。 首先，使用 `answerType` 来标识包含要显示的结果的答案。 然后，使用 `resultIndex` 对答案的结果进行索引，以获取要显示的结果。 （`answerType` 值是 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) 对象中字段的名称。如果不应一起显示答案的所有结果，则排名响应项不应包含 `resultIndex` 字段。

## <a name="ranking-response-example"></a>排名响应示例

下面展示了一个示例 [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)。
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

根据此排名响应，侧栏应显示与 Jimi Hendrix 相关的两个实体结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应实体搜索教程](tutorial-bing-entities-search-single-page-app.md)
