---
title: 必应新闻搜索终结点
titleSuffix: Azure Cognitive Services
description: 新闻搜索 API 终结点摘要。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 82e37e8fa47b467e7c2fe98f801482675809a266
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423782"
---
# <a name="bing-news-search-api-endpoints"></a>必应新闻搜索 API 终结点

“新闻搜索 API”返回新闻文章、网页、图像、视频和[实体](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)。 实体包含有关人员、地点或主题的摘要信息。

## <a name="endpoints"></a>终结点

若要使用必应新闻搜索 API 获取新闻搜索结果，请向以下某个终结点发送 `GET` 请求。 标头和 URL 参数定义了更多规范。

### <a name="news-items-by-search-query"></a>搜索查询的新闻项

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

基于搜索查询返回的新闻项。 如果搜索查询为空，API 将返回不同类别的热门资讯文章。 通过 URL 编码搜索术语发送查询并将其附加到 `q=""` 参数。 有关可用性, 请参阅[支持的国家/地区和市场](language-support.md#supported-markets-for-news-search-endpoint)。

### <a name="top-news-items-by-category"></a>类别的热门新闻项

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

按类别返回热门新闻项。 可以使用 `category=business`、`category=sports` 或 `category=entertainment` 专门请求热门商业、体育或娱乐文章。  `category` 参数只能与 `/news` URL 结合使用。 指定类别有一些正式要求；请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters)文档中的 `category`。 通过 URL 编码搜索术语发送查询并将其附加到 `q=""` 参数。 有关可用性, 请参阅[支持的国家/地区和市场](language-support.md#supported-markets-for-news-endpoint)。

### <a name="trending-news-topics"></a>热门新闻主题 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

返回当前社交网络上热门的新闻主题。 当包含 `/trendingtopics` 选项时，必应搜索会忽略其他几个参数，例如 `freshness` 和 `?q=""`。 有关可用性, 请参阅[支持的国家/地区和市场](language-support.md#supported-markets-for-news-trending-endpoint)。

## <a name="next-steps"></a>后续步骤

有关标头、参数、市场代码、响应对象、错误等的详细信息，请参阅[必应新闻搜索 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 参考。

若要完整了解每个终结点支持的参数，请参阅每种类型对应的参考页面。
有关使用新闻搜索 API 的基本请求的示例，请参阅[必应新闻搜索快速入门](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)。
