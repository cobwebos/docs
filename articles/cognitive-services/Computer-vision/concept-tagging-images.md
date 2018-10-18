---
title: 标记图像 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 标记图像的相关概念。
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 159a1b9404f6ce635dcc8bb6007e52b7346ef98d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982957"
---
# <a name="tagging-images"></a>标记图像

计算机视觉在超过 2000 个可识别对象、生物、风景和操作的基础上返回标记。 如果标记含混不清或者不常见，API 响应会提供“提示”，明确已知设置上下文中的标记的含义。 标记未组织为分类，并且不存在继承层次结构。 内容标记的集合构成在完整句子中显示为用户可读语言的图像“说明”的基础。 请注意，此时图像说明仅支持英语。

上传图像或指定图像 URL 后，计算机视觉算法在对象、生物和图像中标识的操作的基础上输出标记。 标记并不局限于主体（如前景中的人员），还包括设置（室内或室外）、家具、工具、工厂、动物、附件、小工具等。

## <a name="image-tagging-example"></a>图像标记示例

以下 JSON 响应表明计算机视觉在示例图像中检测到标记可视功能时所返回的内容。

![House_Yard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>后续步骤

了解[对图像进行分类](concept-categorizing-images.md)和[描述图像](concept-describing-images.md)的概念。