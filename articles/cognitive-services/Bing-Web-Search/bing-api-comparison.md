---
title: 什么是必应搜索 API？
titleSuffix: Azure Cognitive Services
description: 本文介绍了必应搜索 API，以及如何在应用和服务中启用认知 Internet 搜索。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61431355"
---
# <a name="what-are-the-bing-search-apis"></a>什么是必应搜索 API？

必应搜索 Api 允许您生成 web 连接的应用和服务的查找网页、 图像、 新闻、 位置和不播发的详细信息。 通过使用必应搜索 REST API 或 SDK 发送搜索请求，可以获取 Web 搜索的相关信息和内容。 使用本文章，了解不同的必应搜索 Api 和如何将认知搜索集成到应用程序和服务。 定价和速率限制可能因 API 而异。

## <a name="the-bing-web-search-api"></a>必应 Web 搜索 API

[必应 Web 搜索 API](../Bing-Web-Search/index.yml) 返回网页、图像、视频、新闻等。 您可以筛选发送到此 API 用于包含或排除某些内容类型的搜索查询。

建议在可能需要搜索所有类型相关 Web 内容的应用中使用必应 Web 搜索 API。 如果应用搜索特定类型的联机内容，建议使用以下搜索 API 之一：

## <a name="content-specific-bing-search-apis"></a>内容专用必应搜索 API

从图像、 新闻、 本地商家和视频等 web，以下必应搜索 Api 返回的特定内容。

| 必应 API | 描述 |
| -- | -- |
| [实体搜索](../Bing-Entities-Search/index.yml) | 必应实体搜索 API 返回包含实体的搜索结果，这些实体可以是人、地点或事物。 根据查询，该 API 将返回满足条件的搜索查询的一个或多个实体。 搜索查询可以包含更具吸引力的个人、 本地商家、 地标、 目标和的详细信息。 |
| [图像搜索](../Bing-Image-Search/index.yml) | 必应图像搜索 API 允许你搜索和查找高质量静态和动态映像类似于[Bing.com/images](https://www.Bing.com/images)。 可以将搜索优化为，按属性（包括大小、颜色、许可证和新鲜度）包含或排除图像。 此外，还可以搜索热门图像、上传图像以获取关于图像的见解，并能显示缩略图预览。 |
| [新闻搜索](../Bing-News-Search/index.yml) | 必应新闻搜索 API 使您可以查找新闻故事类似于[Bing.com/news](https://www.Bing.com/news)。 此 API 返回来自多个源或特定域的新闻报道。 可以跨类别搜索，以获取热门报道、头条新闻和头版标题。 |
| [视频搜索](../Bing-Video-Search/index.yml) | 必应视频搜索 API，可在 web 中查找视频。 获取热门视频、相关内容和缩略图预览。 |
| [视觉搜索](../Bing-visual-search/index.yml) | 上传图像或使用 URL 来获取关于图像的见解，如外观相似的产品、图像和相关搜索。 |
 [当地企业搜索](../bing-local-business-search/index.yml) | 必应本地业务搜索 API 可让你查找有关根据搜索查询的本地商家的联系人和位置信息的应用程序。 |

## <a name="the-bing-custom-search-api"></a>必应自定义搜索 API

创建自定义搜索实例与[必应自定义搜索](../Bing-Custom-Search/index.yml)API 可以创建仅侧重于内容和你关注的信息的主题的搜索体验。 例如，指定域、 网站和必应将搜索的特定网页后，必应自定义搜索将调整到该特定内容的结果。 若要进一步自定义搜索体验，可以通过结合使用必应自定义自动建议 API、图像 API 和视频搜索 API。

## <a name="additional-bing-search-apis"></a>其他必应搜索 API

以下必应搜索 Api，可以通过将它们与其他必应搜索 Api 相结合来提高您的搜索体验。

| API | 描述 |
| -- | -- |
| [必应自动建议](../Bing-Autosuggest/index.yml) | 通过在真实时间中返回建议的搜索改进的必应自动推荐 api 的应用程序的搜索体验。  |
| [必应统计信息](bing-web-stats.md) | 必应统计信息 API 分析应用使用的必应搜索 API。 一些可执行的分析包括，调用量、最常用查询字符串和地理分布。 |

## <a name="next-steps"></a>后续步骤

* 必应搜索 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [必应使用和显示要求](./use-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
