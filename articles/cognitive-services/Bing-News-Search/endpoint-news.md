---
title: 必应新闻搜索终结点 | Microsoft Docs
description: 新闻搜索 API 终结点摘要。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: b8ae99698926a818bf22b0b4027af8610413aaa6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365437"
---
# <a name="news-search-endpoints"></a>新闻搜索终结点
“新闻搜索 API”返回新闻文章、网页、图像、视频和[实体](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)。 实体包含有关人员、地点或主题的摘要信息。 
## <a name="endpoints"></a>终结点
若要使用必应 API 获取新闻搜索结果，请向以下某个终结点发送 `GET` 请求。 标头和 URL 参数定义了更多规范。

终结点 1：根据用户的搜索查询返回新闻项。 如果搜索查询为空，则该调用将返回热门新闻文章。 查询 `?q=""` 选项也可以与 `/news` URL 结合使用。 有关可用性，请参阅[支持的国家/地区和市场](supported-countries-markets.md#supported-markets-for-news-search-endpoint)。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search 
``` 

终结点 2：按类别返回热门新闻项。 可以使用 `category=business`、`category=sports` 或 `category=entertainment` 专门请求热门商业、体育或娱乐文章。  `category` 参数只能与 `/news` URL 结合使用。 指定类别有一些正式要求；请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters)文档中的 `category`。 有关可用性，请参阅[支持的国家/地区和市场](supported-countries-markets.md#supported-markets-for-news-endpoint)。 
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

终结点 3：返回当前趋向于社交网络的新闻主题。 当包含 `/trendingtopics` 选项时，必应搜索会忽略其他几个参数，例如 `freshness` 和 `?q=""`。 有关可用性，请参阅[支持的国家/地区和市场](supported-countries-markets.md#supported-markets-for-news-trending-endpoint)。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics 
``` 

有关标头、参数、市场代码、响应对象、错误等的详细信息，请参阅[必应新闻搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) 参考。
## <a name="response-json"></a>响应 JSON
对新闻搜索请求的响应包括作为 JSON 对象的结果。 分析结果需要处理每种类型元素的过程。 有关示例，请参阅[教程](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app)和[源代码](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source)。

## <a name="next-steps"></a>后续步骤
必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。  所有终结点支持按经度、纬度和搜索半径返回特定语言和/或位置的查询。

有关每个终结点支持的参数的完整信息，请参阅每个类型的参考页面。
有关使用新闻搜索 API 的基本请求的示例，请参阅[必应新闻搜索快速入门](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)。