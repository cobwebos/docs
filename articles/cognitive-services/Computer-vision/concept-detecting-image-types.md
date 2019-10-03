---
title: 映像类型检测-计算机视觉
titleSuffix: Azure Cognitive Services
description: 与计算机视觉 API 的图像类型检测功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e89fd977129113fa88af1acccd6b05f0bbe90243
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945209"
---
# <a name="detecting-image-types-with-computer-vision"></a>使用计算机视觉检测图像类型

利用[分析图像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API，计算机视觉可以分析图像的内容类型，指示图像是剪贴画还是线条图。

## <a name="detecting-clip-art"></a>检测剪贴画

计算机视觉可分析图像，并通过 0-3 的量表对图像为剪贴画的可能性进行评级，如下表中所示。

| ReplTest1 | 含义 |
|-------|---------|
| 0 | 非剪贴画 |
| 1 | 不明确 |
| 2 | 正常剪贴画 |
| 3 | 良好剪贴画 |

### <a name="clip-art-detection-examples"></a>剪贴画检测示例

以下 JSON 响应说明了计算机视觉将图像评级为剪贴画的可能性时返回的内容。

![一片奶酪的剪贴画](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![一座蓝色的房子和前院](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>检测线条图

计算机视觉分析图像并返回一个布尔值，该值指示图像是否为线条图。

### <a name="line-drawing-detection-examples"></a>线条图检测示例

以下 JSON 响应说明了计算机视觉指示图像是否为线条图时返回的内容。

![狮子的线描图像](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![具有绿色背景的白色花卉](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>后续步骤

请参阅[分析图像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)参考文档，了解如何检测图像类型。
