---
title: 必应见解的示例 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 本文包含必应可视化搜索如何使用和显示Bing.com的图像见解的示例。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: df66dbeebb04209921ff91c4b99a14580f026718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111636"
---
# <a name="examples-of-bing-insights-usage"></a>必应见解用法示例

本文包含有关必应可能如何使用以及如何在 Bing.com 上显示图像见解的示例。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 见解示例

下面显示指向第一个网页的链接，并允许用户展开和折叠包含图像的其他网页的列表：

![包含扩展页面](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 见解示例

下面显示了必应如何显示图片中看到的产品的购物源：

![购物源](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 见解示例

下面显示了必应如何显示视觉上相似的图像（请参阅示例中**的相关图像**）：

![视觉上相似的图像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 见解示例

以下介绍了必应如何为图像中的食物显示食谱。 该示例让用户知道有可用的配方：

![包含食谱和页面](./media/recipes-pages-including.PNG)

 并在用户展开列表时提供指向配方的链接：

![包含展开的食谱页面](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 见解示例

以下介绍了必应如何显示他人所进行的图像相关搜索。 如果用户单击图像，将针对相关查询转到 Bing.com/images 搜索结果页面。

![图像的相关搜索](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 见解示例

以下介绍了必应如何显示图像中实体（人物、位置或事物）的相关信息。 如果用户单击实体链接，则用户将被带到实体的Bing.com搜索结果页：

![图像中显示的实体](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>显示用户可能浏览的其他见解

以下介绍了必应如何显示用户可能浏览的图像的相关信息。

![浏览有关图像的其他见解](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>边框和热点

非默认标记包括标识图像（标记应用于的图像）中感兴趣区域的边框。 如果边框未标识整个图像，使用边框在图像上创建热点。 用户可以单击热点以获取与热点（或矩形）下内容相关的信息。 例如，如果图像是高时尚图像，则结果可能包含图像中显示的附件的标签（和边界框），例如钱包、珠宝、围巾等。 下面的示例显示了图像中显示的太阳镜的热点矩形：

![边框和热点](./media/click-to-search.PNG)

## <a name="next-steps"></a>后续步骤

要开始使用第一个请求，请参阅快速入门[：C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [节点.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





