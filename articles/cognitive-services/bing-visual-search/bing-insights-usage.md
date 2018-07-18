---
title: 必应见解示例 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bing.com 上显示的图像见解示例。
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: 102bd0e916491738d74956c209829008d779bcbf
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366310"
---
# <a name="examples-of-bing-insights-usage"></a>必应见解用法示例

本部分包含必应如何在 Bing.com 上显示见解的示例。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 见解示例

以下介绍了必应如何显示包含图像的网页。 本示例显示指向第一个网页的链接，并使用户可以展开和折叠包含图像的其他网页列表。

![包含扩展页面](./media/pages-including.PNG)


## <a name="shoppingsources-insight-example"></a>ShoppingSources 见解示例

以下介绍了必应如何显示图像中产品的购物源。

![购物源](./media/shopping-sources.PNG)


## <a name="visualsearch-insight-example"></a>VisualSearch 见解示例

以下介绍了必应如何显示视觉上相似的图像（请参阅示例中的相关图像）。

![视觉上相似的图像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 见解示例

以下介绍了必应如何为图像中的食物显示食谱。 此示例使用户可以知道有可用的食谱。

![包含食谱和页面](./media/recipes-pages-including.PNG)

 当用户展开列表时，提供指向食谱的链接。

![包含展开的食谱页面](./media/expanded-recipes-pages-including.PNG)


## <a name="relatedsearches-insight-example"></a>RelatedSearches 见解示例

以下介绍了必应如何显示他人所进行的图像相关搜索。 如果用户单击图像，将针对相关查询转到 Bing.com/images 搜索结果页面。

![图像的相关搜索](./media/bordered-related-searches.PNG)


## <a name="entity-insight-example"></a>Entity 见解示例

以下介绍了必应如何显示图像中实体（人物、位置或事物）的相关信息。 如果用户单击实体链接，将针对实体转到 Bing.com/images 搜索结果页面。

![图像中显示的实体](./media/entity.PNG)


## <a name="displaying-other-insights-that-the-user-might-explore"></a>显示用户可能浏览的其他见解

以下介绍了必应如何显示用户可能浏览的图像的相关信息。

![浏览有关图像的其他见解](./media/apple-pie-more-tags.PNG)


## <a name="bounding-boxes-and-hot-spots"></a>边框和热点

非默认标记包括标识图像（标记应用于的图像）中感兴趣区域的边框。 如果边框未标识整个图像，使用边框在图像上创建热点。 用户可以单击热点以获取与热点（或矩形）下内容相关的信息。 例如，如果图像是一个高级时装图像，结果可能包含图像中配饰（如钱包、珠宝、围巾）的标记（和边框）。下面示例演示图像中太阳镜的热点矩形。

![边框和热点](./media/click-to-search.PNG)



## <a name="next-steps"></a>后续步骤

要了解这些示例背后的 JSON，请参阅[默认见解](default-insights-tag.md)和 [JSON 响应](overview.md#the-response)。

要快速了解如何使用第一个请求，请参阅快速入门：[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





