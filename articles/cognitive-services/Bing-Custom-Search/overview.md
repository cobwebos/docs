---
title: 什么是必应自定义搜索？ | Microsoft Docs
description: 提供必应自定义搜索的高级概述
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365894"
---
# <a name="what-is-bing-custom-search"></a>什么是必应自定义搜索？

借助必应自定义搜索，可以为关注的主题创建定制的搜索体验。 例如，如果拥有提供搜索体验的网站，则可以指定必应搜索的域、网站和网页。 用户会看到根据他们关注的内容定制的搜索结果，无需浏览包含不相关内容的搜索结果。

若要创建 Web 的自定义视图，请使用必应自定义搜索[门户](https://customsearch.ai)。 通过该门户，你可以创建一个自定义搜索实例，指定你希望必应搜索的域、网站和网页及你不希望必应搜索的网站。 除了指定你了解的内容的 URL 之外，还可以使用该门户查找可能要添加到视图中的相关内容。

如果用户输入特定搜索词，该门户还允许你将特定网页固定到搜索结果的顶部。 

定义实例后，可以通过调用自定义搜索 API 将自定义搜索集成到网站、桌面应用或移动应用。 如果拥有基于 Web 的网站或应用程序，则可以让托管 UI 呈现搜索接口。

下图展示自定义搜索集成的简单易用。

![图片替换文字](./media/bcs-overview.png "必应自定义搜索的工作原理。")

## <a name="customize-search-suggestions"></a>自定义搜索建议

如果已订阅相应级别的自定义搜索（请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)），则可以自定义在自定义搜索体验中显示的搜索建议。 自定义自动建议 API 根据用户提供的部分查询字符串返回建议查询的列表。 借助自定义自动建议，可以提供与搜索体验相关的自定义搜索建议。 指定只返回自定义建议还是包括必应建议。 如果包括必应建议，则自定义建议显示在必应所提供的建议之前。 必应建议仅限于自定义搜索实例的上下文。

## <a name="next-steps"></a>后续步骤

若要快速开始使用，请参阅[创建第一个必应自定义搜索实例](quick-start.md)。

若要深入了解自定义搜索实例时提供的选项，请参阅[定义自定义搜索实例](define-your-custom-view.md)。

自行熟悉[自定义搜索 API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) 参考文档。 此参考文档包含可以用来请求搜索结果的终结点、标头和查询参数的列表。 此外还包含响应对象的定义。

若要了解如何自定义建议，请参阅[定义自定义搜索建议](define-custom-suggestions.md)。

确保阅读[必应使用和显示要求](./use-and-display-requirements.md)，以免违反关于搜索结果使用要求的任何规则。