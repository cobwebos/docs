---
title: 什么是必应自动建议？
titleSuffix: Azure Cognitive Services
description: 必应自动建议 API 根据搜索框中的部分查询字符串返回建议查询的列表。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 5b1e4cc8dfc89d6dcc5d29a368e089402b284352
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072874"
---
# <a name="what-is-bing-autosuggest"></a>什么是必应自动建议？

如果应用程序向任何必应搜索 API 发送查询，可以使用必应自动建议 API 来改进用户的搜索体验。 必应自动建议 API 根据搜索框中的部分查询字符串返回建议查询的列表。 在搜索框中输入字符时，可以在下拉列表中显示建议。

## <a name="bing-autosuggest-api-features"></a>必应自动建议 API 功能

| Feature                                                                                                                                                                                 | 说明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [实时建议搜索词](concepts/get-suggestions.md) | 通过在你键入时使用自动建议 API 显示建议的搜索词来改善应用体验。 |

## <a name="workflow"></a>工作流

必应自动建议 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。
2. 每次用户在应用程序的搜索框中键入新字符时，都会向此 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。

通常，每次用户在应用程序的搜索框中键入新字符时，你都会调用此 API。 输入更多字符时，API 将返回更相关的建议搜索查询。 例如，API 可能为单个 `s` 返回的建议可能不如为 `sail` 返回的建议相关。

下面的示例显示了一个下拉搜索框，其中包含 Bing 自动建议 API 提供的建议查询词。

![自动建议下拉搜索框列表](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

当用户从下拉列表中选择建议时，你可以使用它开始通过必应搜索 API 之一进行搜索，或者直接转到必应搜索结果页。

## <a name="next-steps"></a>后续步骤

若要快速了解如何使用第一个请求，请参阅[创建第一个查询](quickstarts/csharp.md)。

使自己熟悉[必应自动建议 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) 参考文档。 此参考文档包含可以用来请求建议查询词的终结点、标头和查询参数的列表，以及响应对象的定义。

了解如何使用[必应 Web 搜索 API](../bing-web-search/search-the-web.md) 搜索网页。

确保阅读[必应使用和显示要求](./useanddisplayrequirements.md)，以免违反关于搜索结果使用要求的任何规则。
