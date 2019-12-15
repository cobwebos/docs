---
title: 什么是必应 Web 搜索 API？
titleSuffix: Azure Cognitive Services
description: 必应 Web 搜索 API 是一项 RESTful 服务，可以针对用户查询提供即时应答。 搜索结果可以轻松地进行配置，可以包括网页、图像、视频、新闻、翻译等。 结果以 JSON 形式提供，并取决于搜索相关性和必应 Web 搜索订阅。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 12/05/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6615f166ced4a93115f9cbaae1c6ce5b57cf57bc
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892985"
---
# <a name="what-is-the-bing-web-search-api"></a>什么是必应 Web 搜索 API？

必应 Web 搜索 API 是一项 RESTful 服务，可以针对用户查询提供即时应答。 搜索结果可以轻松地进行配置，可以包括网页、图像、视频、新闻、翻译等。 必应 Web 搜索根据搜索相关性和必应 Web 搜索订阅以 JSON 的形式提供结果。

此 API 适用于需要访问的所有内容都是与用户的搜索查询相关的应用程序。 如果要生成的应用程序只需特定类型的结果，可以考虑使用[必应图像搜索 API](../Bing-Image-Search/overview.md)、[必应视频搜索 API](../Bing-Video-Search/search-the-web.md) 或[必应新闻搜索 API](../Bing-News-Search/search-the-web.md)。 如需必应搜索 API 的完整列表，请参阅[认知服务 API](https://docs.microsoft.com/azure/cognitive-services)。

想要了解其工作原理？ 尝试[必应 Web 搜索 API 演示](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)。

## <a name="features"></a>功能  

必应 Web 搜索不仅能让你访问即时答案， 它还提供其他特性和功能，用于为用户自定义搜索结果。

| Feature | 说明 |
|---------|-------------|
| [以实时方式建议搜索词](../bing-autosuggest/get-suggested-search-terms.md) | 改进应用程序体验，方法是在用户键入时通过必应自动建议 API 显示建议的搜索词。 |
| [通过内容类型筛选和限制结果](filter-answers.md) | 针对网页、图像、视频、安全搜索等使用筛选器和查询参数自定义和优化搜索结果。 |
| [针对 unicode 字符的命中词突出显示](hit-highlighting.md) | 在搜索结果中标识并删除不需要的 unicode 字符，然后再以命中词突出显示方式将其显示给用户。 |
| [按国家、地区和/或市场将搜索结果本地化](supported-countries-markets.md) | 必应 Web 搜索支持的国家或地区超过 36 个。 使用此功能按特定的国家/地区或市场优化搜索结果。 |
| [使用必应统计信息分析搜索指标](bing-web-stats.md) | 必应统计信息是一款付费订阅，可以针对调用量、最常用查询字符串、地理分布等进行分析。 |

## <a name="workflow"></a>工作流

可以轻松地通过任何编程语言调用必应 Web 搜索 API，只要该语言能够发出 HTTP 请求和分析 JSON 响应即可。 此服务可以通过 [REST API](quickstarts/python.md) 或[必应 Web 搜索 SDK](web-sdk-python-quickstart.md) 进行访问。

1. 为必应搜索 API [创建 Azure 资源](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。  
2. [向必应 Web 搜索 API 发送请求](quickstarts/python.md)。
3. 分析 JSON 响应。

## <a name="next-steps"></a>后续步骤

* 根据 [Python 快速入门](quickstarts/python.md)中的说明对必应 Web 搜索 API 进行第一次调用。  
* [生成单页 Web 应用](tutorial-bing-web-search-single-page-app.md)。
* 查看 [Web 搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)文档。  
* 详细了解必应 Web 搜索的[使用和显示要求](UseAndDisplayRequirements.md)。  
