---
title: 对图像进行分类 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解与计算机视觉 API 的图像分类功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 40873b13407066920caec5c04751b65c01d7e209
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579492"
---
# <a name="image-categorization-with-computer-vision"></a>使用计算机视觉进行图像分类

除了标记和说明以外，计算机视觉还返回早期版本中定义的基于分类的类别。 这些类别组织为具有父/子遗传的层次结构的分类。 所有类别均采用英语。 它们可单独使用或与我们的新标记模型结合使用。

## <a name="the-86-category-concept"></a>86 类别概念

根据下图中所示的包含 86 个概念的列表，可以对图像按广泛到具体进行分类。 有关文本格式的完整分类，请参阅[类别分类](category-taxonomy.md)。

![类别分类中所有类别的分组列表](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>图像分类示例

以下 JSON 响应表明计算机视觉在基于视觉特征对示例图像进行分类时返回的内容。

![在公寓楼顶上的一个女人](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

下表说明了典型的图像集以及计算机视觉为每个图像返回的类别。

| 映像 | 类别 |
|-------|----------|
| ![四个人一起摆出一家人的姿势](./Images/family_photo.png) | people_group |
| ![一只小狗坐在草地上](./Images/cute_dog.png) | animal_dog |
| ![日落时站在山岩上的人](./Images/mountain_vista.png) | outdoor_mountain |
| ![桌子上有一堆面包](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>后续步骤

了解[标记图像](concept-tagging-images.md)和[描述图像](concept-describing-images.md)的概念。