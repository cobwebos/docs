---
title: 对必应缩略图执行重设大小和裁剪 | Microsoft Docs
description: 介绍了如何对必应响应结果中包含的缩略图执行重设大小和裁剪。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 98c4caa50ca5e861f4276e26983ef501d17bd349
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929743"
---
# <a name="resizing-and-cropping-thumbnail-images"></a>对必应缩略图执行重设大小和裁剪

在处理搜索查询时，必应将为其[响应](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format)中的所有图像生成缩略图信息。 此信息可用于显示所有或部分返回的缩略图。 如果显示一部分缩略图，请提供查看剩余图像的选项。 


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

如果用户单击缩略图，则可使用 [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) 向用户显示全尺寸图像。 确保标识图像的归属。

如果 `shoppingSourcesCount` 或 `recipeSourcesCount` 大于零，请向缩略图添加标记（例如购物车），说明图像中存在商品或食品。

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

若要获取图像的见解，例如包含图像的网页或在图像中识别的人物，请使用 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken)。 有关详细信息，请参阅[图像见解](image-insights.md)。

## <a name="resizing-and-cropping-thumbnails"></a>对缩略图进行重设大小和裁剪

还可以对缩略图重设大小和展开缩略图，例如当用户的光标悬停在其上方时。 
> [!NOTE]
> 确保在展开图像时标识图像的归属。 例如，可以从 [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) 提取主机，然后将其显示在图像下方。 

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]