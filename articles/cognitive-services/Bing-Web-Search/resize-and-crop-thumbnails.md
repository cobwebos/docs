---
title: 重设图像缩略图大小并进行剪裁 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 必应搜索 API 中的一些答案包括必应提供的缩略图图像的 URL，您可以调整大小并裁剪这些缩略图，并可能包含查询参数。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110639"
---
# <a name="resize-and-crop-thumbnail-images"></a>调整和裁剪缩略图图像

必应搜索 API 中的一些答案包括必应提供的缩略图图像的 URL，您可以调整大小并裁剪这些缩略图，并可能包含查询参数。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果显示这些缩略图的子集，请提供查看其余图像的选项。

> [!NOTE]
> 确保裁剪和调整缩略图图像大小将提供一个搜索方案，尊重第三方权限，这是必应搜索 API[使用和显示要求的要求](use-display-requirements.md)。

## <a name="resize-a-thumbnail"></a>调整缩略图的大小 

要调整缩略图的大小，必应建议您只指定缩`w`略图 URL`h`中的一个（宽度）或（高度）查询参数。 仅指定高度或宽度，Bing 可以维护图像的原始方面。 以像素为单位指定宽和高。 

例如，如果原始缩略图为 480x620：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

您希望减小其大小，将`w`参数设置为新值（例如`336`），并删除参数： `h`

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果仅指定缩略图的高度或宽度，则将保留图像的原始纵横比。 如果同时指定两个参数，并且纵横比未保持，必应将添加白色填充到图像的边框。

例如，如果将 480x359 图像的大小调整到 200x200 而不进行裁剪，则全宽将包含图像，但高度将在图像的顶部和底部包含 25 像素的白色填充。 如果图像为 359x480，则左右边框将包含白色填充。 如果裁剪图像，不会添加白色填充内容。  

下图显示缩略图的原始大小 (480x300)。  
  
![原始风景图](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下图显示调整为 200x200 大小后的图像。 保持纵横比，顶部和底部边框用白色填充（此处的黑色边框包括显示填充）。  
  
![调整大小后的风景图](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果指定的尺寸大于图像的原始宽度和高度，必应将添加白色填充到左侧和顶部边框。  

## <a name="request-different-thumbnail-sizes"></a>请求不同的缩略图大小

要请求不同的缩略图图像大小，请从缩略图的 URL 中删除所有查询参数`id`，`pid`除了 和 参数。 然后添加`&w`（宽度）或`&h`（高度）查询参数，所需图像大小以像素为单位，但不能同时添加两者。 必应将保持图像的原始纵横比。 

要将上述 URL 指定的图像宽度增加到 165 像素，请使用以下 URL：

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果请求的图像大于图像的原始大小，必应根据需要在图像周围添加白色填充。 例如，如果图像的原始大小为 474x316，而您设置为`&w`500，必应将返回 500x33 图像。 此图像沿上边缘和下边缘将具有 8.5 像素的白色填充，在左右边缘有 13 像素的填充。

为防止必应添加白色填充（如果请求的大小大于图像的原始大小），请将`&p`查询参数设置为 0。 例如，如果在上述 URL`&p=0`中包含参数，必应将返回 474x316 图像，而不是 500x33 图像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果同时指定和`&w``&h`查询参数，必应将维护图像的纵横比并根据需要添加白色填充。 例如，如果图像的原始大小为 474x316，并且将宽度和高度参数设置为 200x200 （），`&w=200&h=200`必应返回顶部和底部包含 33 像素白色填充的图像。 如果包含`&p`查询参数，必应将返回 200x134 图像。

## <a name="crop-a-thumbnail"></a>裁剪缩略图 

要裁剪图像，`c`请包括（裁剪）查询参数。 可以使用下列值：
  
- `4`&mdash;盲比  
- `7`&mdash;智能比  

### <a name="smart-ratio-cropping"></a>智能比率裁剪

如果请求智能比率裁剪（通过将`c`参数设置为`7`），必应将从其感兴趣区域的中心向外裁剪图像，同时保持图像的纵横比。 感兴趣区域是图像中被必应确定为包含最重要内容的区域。 下图是感兴趣区域的示例。  
  
![感兴趣区域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果调整图像大小并请求智能比率裁剪，必应将图像减小到请求的大小，同时保持纵横比。 然后，必应根据调整大小的尺寸裁剪图像。 例如，如果调整大小的宽度小于或等于高度，必应将图像裁剪到感兴趣区域中心的左侧和右侧。 否则，必应将裁剪到感兴趣区域中心的顶部和底部。  
  
 
下图显示使用“智能比例”裁剪将图像缩成 200x200 大小的情形。 由于必应从左上角测量图像，因此将裁剪图像的底部。 
  
![裁剪成 200x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下图显示使用“智能比例”裁剪将图像缩成 200x100 大小的情形。 由于必应从左上角测量图像，因此将裁剪图像的底部。 
   
![裁剪成 200x100 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下图显示使用“智能比例”裁剪将图像缩成 100x200 大小的情形。 由于必应从中心测量图像，因此将裁剪图像的左侧和右侧部分。
  
![裁剪成 100x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果必应无法确定图像的感兴趣区域，则服务将使用盲比率裁剪。  

### <a name="blind-ratio-cropping"></a>盲比裁剪

如果请求盲比率裁剪（通过将`c`参数设置为`4`），必应使用以下规则裁剪图像。  
  
- 如果`(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`从左上角测量图像，并在底部裁剪。  
- 如果`(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`从中心测量图像，然后裁剪到左侧和右侧。  

下面是 225x300 大小的纵向图。  
  
![原始向日葵图](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x200 大小的情形。 从左上角测量图像，导致裁剪图像的底部。  
  
![裁剪成 200x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x100 大小的情形。 从左上角测量图像，导致裁剪图像的底部。  
  
![裁剪成 200x100 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 100x200 大小的情形。 图像从中心进行测量，因此会裁剪其左侧和右侧。  
  
![裁剪成 100x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>后续步骤

* [什么是必应搜索 API？](bing-api-comparison.md)
* [必应搜索 API 使用和显示要求](use-display-requirements.md)
