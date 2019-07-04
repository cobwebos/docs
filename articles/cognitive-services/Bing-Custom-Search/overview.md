---
title: 什么是必应自定义搜索 API？
titlesuffix: Azure Cognitive Services
description: 借助必应自定义搜索 API，可为关注的主题创建定制的搜索体验。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 067693c3c02d19f3bdab77f315c920b25078e7f5
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542695"
---
# <a name="what-is-the-bing-custom-search-api"></a>什么是必应自定义搜索 API？

借助必应自定义搜索 API，可为关注的主题创建定制的无广告搜索体验。 可以指定要让必应搜索的域和网页，并可以固定、提升或降级特定的内容以创建自定义的 Web 视图，帮助用户快速找到相关的搜索结果。 

## <a name="features"></a>功能

|Feature  |说明  |
|---------|---------|
|[自定义实时搜索建议](define-custom-suggestions.md)     | 当用户键入搜索词时，以下拉列表的形式提供搜索建议。       | 
|[自定义图像搜索体验](get-images-from-instance.md)     | 让用户从自定义搜索实例中指定的域和网站搜索图像。        |        
|[自定义视频搜索体验](get-videos-from-instance.md)     | 让用户从自定义搜索实例中指定的域和站点搜索视频。        |    
|[共享自定义搜索实例](share-your-custom-search.md)     | 将搜索实例与团队成员共享，以合作方式对其进行编辑和测试。        | 
|[配置应用程序和网站的 UI](hosted-ui.md)     | 将搜索实例与团队成员共享，以合作方式对其进行编辑和测试。        | 
## <a name="workflow"></a>工作流

可以使用[必应自定义搜索门户](https://customsearch.ai)创建自定义的搜索实例。 通过该门户可以创建自定义的搜索实例，用于指定要让必应搜索的域、网站和网页，以及你不希望必应搜索的内容。 使用门户还可以：预览搜索体验、调整 API 提供的搜索排名，并选择性地配置一个可在网站和应用程序中呈现的可搜索用户界面。

创建搜索实例后，可以通过调用必应自定义搜索 API 将它（和可选的用户界面）集成到网站或应用程序中：

![显示可以通过 API 连接到必应自定义搜索的图像](media/BCS-Overview.png "必应自定义搜索的工作原理。")


## <a name="next-steps"></a>后续步骤

若要快速开始使用，请参阅[创建第一个必应自定义搜索实例](quick-start.md)。

若要深入了解自定义搜索实例，请参阅[定义自定义搜索实例](define-your-custom-view.md)。

请务必阅读[必应用法和显示要求](./use-and-display-requirements.md)，了解如何在服务和应用程序中使用搜索结果。

了解每个自定义搜索终结点的参考内容。 该参考内容包含用于请求搜索结果的终结点、标头和查询参数。 此外还包含响应对象的定义。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [自定义搜索 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [自定义图像 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [自定义视频 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [自定义自动建议 API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

