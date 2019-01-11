---
title: 描述图像 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与计算机视觉 API 的图像描述功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5b920e4ce8df131b81a9ef6ce2d66c7082d8f5e4
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583402"
---
# <a name="describe-images-with-human-readable-language"></a>使用人类可读语言描述图像

计算机视觉的算法可分析图像中的内容。 此分析构成在完整句子中显示为用户可读语言的“说明”的基础。 该说明概述了图像中找到的内容。 计算机视觉的算法可根据图像中标识的视觉特征生成各种说明。 每项说明都经过评估并带有置信度分数。 然后将返回置信度分数从高到低的列表。

## <a name="image-description-example"></a>图像说明示例

以下 JSON 响应表明计算机视觉在基于视觉特征对示例图像进行描述时返回的内容。

![曼哈顿建筑的黑白照片](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[对图像进行分类](concept-categorizing-images.md)的概念。