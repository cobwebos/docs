---
title: 什么是必应自定义搜索？
titlesuffix: Azure Cognitive Services
description: 提供必应自定义搜索的高级概述。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: f2946918f0c1a7a516788989042825e8f49d7b0b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318718"
---
# <a name="what-is-bing-custom-search"></a>什么是必应自定义搜索？

借助必应自定义搜索，可以为关注的主题创建定制的搜索体验。 例如，如果拥有提供搜索体验的网站，则可以指定必应搜索的域、网站和网页。 用户会看到根据他们关注的内容定制的搜索结果，无需浏览包含不相关内容的搜索结果。

若要创建 Web 的自定义视图，请使用必应自定义搜索[门户](https://customsearch.ai)。 通过该门户，你可以创建一个自定义搜索实例，指定你希望必应搜索的域、网站和网页及你不希望必应搜索的网站。 除了指定你了解的内容的 URL 之外，还可以使用该门户查找可能要添加到视图中的相关内容。

如果用户输入特定搜索词，该门户还允许你将特定网页固定到搜索结果的顶部。 

定义实例后，可以通过调用自定义搜索 API 将自定义搜索集成到网站、桌面应用或移动应用。 如果拥有基于 Web 的网站或应用程序，则可以让托管 UI 呈现搜索接口。

下图展示自定义搜索集成的简单易用。

![图片替换文字](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/cognitive-services/Bing-Custom-Search/media/BCS-Overview.png "必应自定义搜索的工作原理。")

## <a name="adding-custom-search-box-suggestions"></a>添加自定义搜索框建议

通过定义搜索框建议可丰富自定义搜索体验。 此功能可提供与搜索体验相关的自定义搜索建议。 用户在搜索框中键入内容时，下拉列表会根据用户键入的部分查询字符串提供查询字符串建议。 可指定是仅返回自定义建议还是包含必应建议。 [了解详细信息](define-custom-suggestions.md)。

## <a name="adding-custom-image-search-experience"></a>添加自定义图像搜索体验

可通过图像丰富自定义搜索体验。 与 Web 结果类似，自定义搜索支持在实例的网站列表中搜索图像。 [了解详细信息](get-images-from-instance.md)。

## <a name="adding-custom-video-search-experience"></a>添加自定义视频搜索体验

可通过视频来丰富自定义搜索体验。 与 Web 结果类似，自定义搜索支持在实例的网站列表中搜索视频。 [了解详细信息](get-videos-from-instance.md)。

## <a name="sharing-your-custom-search-instance-with-others"></a>与他人共享自定义搜索实例

与团队成员共享实例，即可轻松实现对实例的协作编辑和测试。 [了解详细信息](share-your-custom-search.md)。

## <a name="next-steps"></a>后续步骤

若要快速开始使用，请参阅[创建第一个必应自定义搜索实例](quick-start.md)。

若要深入了解自定义搜索实例，请参阅[定义自定义搜索实例](define-your-custom-view.md)。

了解每个自定义搜索终结点的参考内容。 该参考内容包含用于请求搜索结果的终结点、标头和查询参数。 此外还包含响应对象的定义。

- [自定义搜索 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [自定义图像 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [自定义视频 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [自定义自动建议 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


确保阅读[必应使用和显示要求](./use-and-display-requirements.md)，以免违反关于搜索结果使用要求的任何规则。
