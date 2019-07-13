---
title: 重设图像缩略图大小并进行剪裁 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何调整大小和裁剪缩略图提供的必应搜索 Api。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867878"
---
# <a name="resize-and-crop-thumbnail-images"></a>调整和裁剪缩略图图像

从必应搜索 Api 某些问题的答案包括到由必应，您可以调整大小和裁剪，提供服务的缩略图图像的 Url，并可能包含查询参数。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果显示这些缩略图的子集，请提供一个选项以查看剩余的图像。

> [!NOTE]
> 确保裁剪和调整缩略图图像的大小将提供了搜索方案尊重第三方权利，所需的必应搜索 API[使用和显示要求](use-display-requirements.md)。

## <a name="resize-a-thumbnail"></a>调整缩略图大小 

若要调整缩略图的大小，必应建议您指定只有一个`w`（宽度） 或`h`（高度） 缩略图的 URL 中的查询参数。 指定的高度或宽度允许必应保持图像的原始长宽。 以像素为单位指定宽和高。 

例如，如果原始的缩略图 480 x 620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

并且你想要减小其大小，请将`w`参数为新值 (例如`336`)，并删除`h`参数：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果指定的高度或宽度的缩略图时，将保留图像的原始纵横比。 如果指定了这两个参数，并且不保留纵横比，必应会将白色填充内容添加到图像的边框。

例如，如果调整成 200x200 480x359 图像大小不裁剪的情况下，全角字符将包含映像但高度将包含 25 像素的白色填充内容的顶部和底部的图像。 如果图像的 359 x 480，左和右边框将包含白色填充内容。 如果裁剪图像，不会添加白色填充内容。  

下图显示缩略图的原始大小 (480x300)。  
  
![原始风景图](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下图显示调整为 200x200 大小后的图像。 保持纵横比和顶部和底部边框填充了白色 （黑色边框是为了显示填充内容）。  
  
![调整大小后的风景图](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果您指定尺寸大于图像的原始宽度和高度，必应会将白色填充内容添加到左侧和顶部边框。  

## <a name="request-different-thumbnail-sizes"></a>请求不同的缩略图大小

若要请求不同的缩略图大小，删除所有查询参数从缩略图的 URL，除`id`和`pid`参数。 然后添加`&w`（宽度） 或`&h`（高度） 查询参数的所需的图像大小，以像素为单位，但不是能同时使用。 必应将保持图像的原始纵横比。 

若要增加到 165 像素上述 URL 指定图像的宽度，将使用以下 URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果请求大于图像的原始大小的图像，必应根据需要添加在图像周围的白色填充内容。 例如，如果图像的原始大小为 474 x 316 并且设置`&w`为 500，必应将返回 500 x 333 映像。 此映像将具有 8.5 像素的白色填充沿顶部和底部边缘和 13 的左侧和右侧边缘上填充的像素为单位。

若要防止必应添加白色填充内容，如果所请求的大小大于图像的原始大小，设置`&p`查询参数设为 0。 例如，如果您包括`&p=0`上述 URL，必应中的参数将返回而不是 500 x 333 映像 474 x 316 映像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果同时指定`&w`和`&h`查询参数，必应将保持图像的纵横比，并根据需要添加白色填充内容。 例如，如果图像的原始大小是 474 x 316 并将宽度和高度参数设置为 200x200 (`&w=200&h=200`)，必应返回包含白色填充内容上的顶部和底部的 33 像素的图像。 如果包含`&p`查询参数，必应会返回 200 x 134 图像。

## <a name="crop-a-thumbnail"></a>裁剪缩略图 

若要裁剪图像，包括`c`（裁剪） 查询参数。 可以使用以下值：
  
- `4` &mdash; 盲比例  
- `7` &mdash; 智能比例  

### <a name="smart-ratio-cropping"></a>智能裁剪比率

如果请求智能比例裁剪 (通过设置`c`参数`7`)，必应将裁剪将图像从其感兴趣的区域的中心向外，同时保持图像的纵横比。 感兴趣区域是图像中被必应确定为包含最重要内容的区域。 下图是感兴趣区域的示例。  
  
![感兴趣区域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果调整图像大小，并请求智能比例裁剪，必应会使图像到请求的大小减少同时保持纵横比。 必应然后裁剪该图像基于已调整大小的尺寸。 例如，如果调整后的宽度小于或等于高度，必应将裁剪图像左侧和右侧的感兴趣的区域的中心。 否则，必应会裁剪其为顶部和底部的感兴趣的区域的中心。  
  
 
下图显示使用“智能比例”裁剪将图像缩成 200x200 大小的情形。 必应测量左上角中的映像，因为裁剪该图像的底部。 
  
![裁剪成 200x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下图显示使用“智能比例”裁剪将图像缩成 200x100 大小的情形。 必应测量左上角中的映像，因为裁剪该图像的底部。 
   
![裁剪成 200x100 大小的风景图](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下图显示使用“智能比例”裁剪将图像缩成 100x200 大小的情形。 必应测量从中心的映像，因为进行裁剪图像的左侧和右侧的部分。
  
![裁剪成 100x200 大小的风景图](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果无法确定图像的感兴趣的区域，该服务将使用盲比例裁剪。  

### <a name="blind-ratio-cropping"></a>盲比例裁剪

如果请求盲比例裁剪 (通过设置`c`参数`4`)，必应使用以下规则来裁剪图像。  
  
- 如果`(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`，从左上角测量图像，并在底部进行裁剪。  
- 如果`(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`，从中心测量图像并将其裁剪为左侧和右侧。  

下面是 225x300 大小的纵向图。  
  
![原始向日葵图](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x200 大小的情形。 图像从导致的映像，因此会裁剪其底部的左上角进行测量。  
  
![裁剪成 200x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 200x100 大小的情形。 图像从导致的映像，因此会裁剪其底部的左上角进行测量。  
  
![裁剪成 200x100 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下图显示使用“盲比例”裁剪将图像缩成 100x200 大小的情形。 图像从中心进行测量，因此会裁剪其左侧和右侧。  
  
![裁剪成 100x200 大小的向日葵图](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>后续步骤

* [必应搜索 Api 有哪些？](bing-api-comparison.md)
* [必应搜索 API 使用和显示要求](use-display-requirements.md)
