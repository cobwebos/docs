---
title: 什么是必应搜索 API？
titleSuffix: Azure Cognitive Services
description: 本文介绍了必应搜索 API，以及如何在应用和服务中启用认知 Internet 搜索。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775534"
---
# <a name="what-are-the-bing-search-apis"></a>什么是必应搜索 API？

必应搜索 API 使你可以构建 web 连接的应用程序和服务，这些应用程序和服务可查找网页、图像、新闻、位置，并且无需播发。 通过使用必应搜索 REST API 或 SDK 发送搜索请求，可以获取 Web 搜索的相关信息和内容。 使用本文了解各种必应搜索 Api，以及如何将认知搜索集成到你的应用程序和服务中。 定价和速率限制可能因 API 而异。

## <a name="the-bing-web-search-api"></a>必应 Web 搜索 API

[必应 Web 搜索 API](../Bing-Web-Search/overview.md) 返回网页、图像、视频、新闻等。 你可以筛选发送到此 API 的搜索查询，以包含或排除某些内容类型。

建议在可能需要搜索所有类型相关 Web 内容的应用中使用必应 Web 搜索 API。 如果应用搜索特定类型的联机内容，建议使用以下搜索 API 之一：

## <a name="content-specific-bing-search-apis"></a>内容专用必应搜索 API

以下必应搜索 Api 从 web 返回特定内容，如图像、新闻、本地企业和视频。

| 必应 API | 描述 |
| -- | -- |
| [实体搜索](../Bing-Entities-Search/overview.md) | 必应实体搜索 API 返回包含实体的搜索结果，这些实体可以是人、地点或事物。 根据查询，API 将返回满足搜索查询的一个或多个实体。 搜索查询可以包括值得注意的个人、本地企业、特征点、目标等。 |
| [图像搜索](../Bing-Image-Search/overview.md) | 必应图像搜索 API 使你可以搜索和查找类似于[Bing.com/images](https://www.Bing.com/images)的高质量静态图像和动画图像。 可以将搜索优化为，按属性（包括大小、颜色、许可证和新鲜度）包含或排除图像。 此外，还可以搜索热门图像、上传图像以获取关于图像的见解，并能显示缩略图预览。 |
| [新闻搜索](../Bing-News-Search/search-the-web.md) | 必应新闻搜索 API 允许你查找类似于[Bing.com/news](https://www.Bing.com/news)的新闻报道。 此 API 返回来自多个源或特定域的新闻报道。 可以跨类别搜索，以获取热门报道、头条新闻和头版标题。 |
| [视频搜索](../Bing-Video-Search/overview.md) | 必应视频搜索 API 允许你在 web 上查找视频。 获取热门视频、相关内容和缩略图预览。 |
| [视觉搜索](../Bing-visual-search/overview.md) | 上传图像或使用 URL 来获取关于图像的见解，如外观相似的产品、图像和相关搜索。 |
 [当地企业搜索](../bing-local-business-search/overview.md) | 必应本地业务搜索 API 允许应用程序根据搜索查询查找有关本地企业的联系信息和位置信息。 |

## <a name="the-bing-custom-search-api"></a>必应自定义搜索 API

通过使用[必应自定义搜索](../Bing-Custom-Search/overview.md)API 创建自定义搜索实例，你可以创建仅侧重于你关注的内容和主题的搜索体验。 例如，指定必应搜索的域、网站和特定网页后，必应自定义搜索会将结果定制为特定内容。 若要进一步自定义搜索体验，可以通过结合使用必应自定义自动建议 API、图像 API 和视频搜索 API。

## <a name="additional-bing-search-apis"></a>其他必应搜索 API

以下必应搜索 API 使你可以通过将其与其他必应搜索 Api 相结合来改善搜索体验。

| API | 描述 |
| -- | -- |
| [必应自动建议](../Bing-Autosuggest/get-suggested-search-terms.md) | 通过以实时方式返回建议的搜索，使用必应自动推荐 API 提高应用程序的搜索体验。  |
| [必应统计信息](bing-web-stats.md) | 必应统计信息 API 分析应用使用的必应搜索 API。 一些可执行的分析包括，调用量、最常用查询字符串和地理分布。 |

## <a name="next-steps"></a>后续步骤

* 必应搜索 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [必应使用和显示要求](./use-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
