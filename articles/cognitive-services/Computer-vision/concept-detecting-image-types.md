---
title: 检测图像类型 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 检测图像类型的相关概念。
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 1a76106caed514f56e897203fba5215b7e93d4ff
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984809"
---
# <a name="detecting-image-types"></a>检测图像类型

计算机视觉通过指示图像是否为剪贴画或线条图并根据量表对可能性进行评级来分析图像的内容类型。

## <a name="detecting-clip-art"></a>检测剪贴画

计算机视觉可分析图像，并通过 0-3 的量表对图像为剪贴画的可能性进行评级，如下表中所示。

| 值 | 含义 |
|-------|---------|
| 0 | 非剪贴画 |
| 1 | 不明确 |
| 2 | 正常剪贴画 |
| 3 | 良好剪贴画 |

### <a name="clip-art-detection-examples"></a>剪贴画检测示例

以下 JSON 响应说明了计算机视觉将图像评级为剪贴画的可能性时返回的内容。

![视觉分析奶酪剪贴画](./Images/cheese_clipart.png)

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

![视觉分析住宅庭院](./Images/house_yard.png)

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

![视觉分析狮子图](./Images/lion_drawing.png)

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

![视觉分析花](./Images/flower.png)

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

了解[标记图像](concept-tagging-images.md)和[对图像进行分类](concept-categorizing-images.md)的概念。