---
title: 什么是必应搜索 API？
titleSuffix: Azure Cognitive Services
description: 本文介绍了必应搜索 API，以及如何在应用和服务中启用认知 Internet 搜索。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: b2277f540b076307fe74c57068ff288860f82796
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513083"
---
# <a name="what-are-the-bing-search-apis"></a>什么是必应搜索 API？

使用必应搜索 API，可以生成不含广告的联网应用和服务，用于查找网页、图像、新闻、位置等。 通过使用必应搜索 REST API 或 SDK 发送搜索请求，可以获取 Web 搜索的相关信息和内容。 本文介绍了各种必应搜索 API，以及如何将认知搜索集成到应用和服务中。 定价和速率限制可能因 API 而异。

## <a name="the-bing-web-search-api"></a>必应 Web 搜索 API

[必应 Web 搜索 API](../Bing-Web-Search/index.yml) 返回网页、图像、视频、新闻等。 可以将发送到此 API 的搜索查询筛选为，包含或排除特定内容类型。

建议在可能需要搜索所有类型相关 Web 内容的应用中使用必应 Web 搜索 API。 如果应用搜索特定类型的联机内容，建议使用以下搜索 API 之一： 

## <a name="content-specific-bing-search-apis"></a>内容专用必应搜索 API

以下必应搜索 API 返回特定网上内容，如图像、新闻、当地企业和视频。

| 必应 API | 说明 |
| -- | -- | 
| [实体搜索](../Bing-Entities-Search/index.yml) | 必应实体搜索 API 返回包含实体的搜索结果，这些实体可以是人、地点或事物。 此 API 会根据查询返回一个或多个与搜索查询匹配的实体，这些实体可以包括值得关注的个人、当地企业、地标、目的地等。 |
| [图像搜索](../Bing-Image-Search/index.yml) | 使用必应图像搜索 API，可以搜索和查找高质量的静态图像和动态图像（如 [Bing.com/images](https://www.Bing.com/images)）。 可以将搜索优化为，按属性（包括大小、颜色、许可证和新鲜度）包含或排除图像。 此外，还可以搜索热门图像、上传图像以获取关于图像的见解，并能显示缩略图预览。 |
| [新闻搜索](../Bing-News-Search/index.yml) | 使用必应新闻搜索 API，可以查找新闻报道（如 [Bing.com/news](https://www.Bing.com/news)）。 此 API 返回来自多个源或特定域的新闻报道。 可以跨类别搜索，以获取热门报道、头条新闻和头版标题。
| [视频搜索](../Bing-Video-Search/index.yml) | 使用必应视频搜索 API，可以跨 Web 查找视频。 获取热门视频、相关内容和缩略图预览。 |
| [视觉搜索](../Bing-visual-search/index.yml) | 上传图像或使用 URL 来获取关于图像的见解，如外观相似的产品、图像和相关搜索。 |
 [当地企业搜索](../bing-local-business-search/index.yml) | 必应当地企业搜索 API 使应用程序可以基于搜索查询查找有关当地企业的联系人和位置信息。 |

## <a name="the-bing-custom-search-api"></a>必应自定义搜索 API

使用[必应自定义搜索](../Bing-Custom-Search/index.yml) API 创建自定义搜索实例，可以打造仅以你关心的内容和主题为重点的搜索体验。 例如，在你指定必应将搜索的域、网站和特定网页之后，结果会根据特定内容进行调整。 若要进一步自定义搜索体验，可以通过结合使用必应自定义自动建议 API、图像 API 和视频搜索 API。  

## <a name="additional-bing-search-apis"></a>其他必应搜索 API

通过结合使用以下必应搜索 API 和其他必应搜索 API，可以提升搜索体验。

| API | 说明 |
| -- | -- | 
| [必应自动建议](../Bing-Autosuggest/index.yml) | 通过实时返回建议的搜索，使用必应自动建议 API 提升应用的搜索体验。  |
| [必应统计信息](bing-web-stats.md) | 必应统计信息 API 分析应用使用的必应搜索 API。 一些可执行的分析包括，调用量、最常用查询字符串和地理分布。 |

## <a name="next-steps"></a>后续步骤

* 必应搜索 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [必应使用和显示要求](./use-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
