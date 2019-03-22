---
title: 对图像进行分类 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解与计算机视觉 API 的图像分类功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 07fdaa22532f48cc39b6c524d85fdfe625f8b80c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337117"
---
# <a name="categorize-images"></a>对图像进行分类

除了标记和说明以外，计算机视觉还返回图像中检测到的基于分类的类别。 不同于标记，类别是在父/子继承层次结构中组织的，并且数量更少（86 个，与数千个标记截然相反）。 所有类别名称均采用英语。 它可以单独完成分类，也可以与新的标记模型共同完成。

## <a name="the-86-category-concept"></a>86 类别概念

计算机视觉可以对分类图像广泛或特别是，使用下面的关系图中的 86 种类别的列表。 有关文本格式的完整分类，请参阅[类别分类](category-taxonomy.md)。

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
