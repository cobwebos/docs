---
title: 检测人脸 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与计算机视觉 API 的人脸检测功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1056b8be113d56342aea8f83d5325737f7ecb93b
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308445"
---
# <a name="face-detection-with-computer-vision"></a>使用计算机视觉进行人脸检测

计算机视觉可检测图像中的人脸，并为每个检测到的人脸生成年龄、性别和人脸边框。 

> [!NOTE]
> Azure [人脸](/azure/cognitive-services/face/)服务也提供此功能。 有关人脸分析（包括人脸识别和姿势检测）的详细信息，请参阅此替代方案。 

## <a name="face-detection-examples"></a>人脸检测示例

以下示例演示了计算机视觉为包含单个人脸的图像返回的 JSON 响应。

![视觉分析女士屋顶人脸](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

第二个示例演示了为包含多个人脸的图像返回的 JSON 响应。

![视觉分析家庭照片中的人脸](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>后续步骤

有关如何使用人脸检测功能的详细信息，请参阅[分析图像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)参考文档。
