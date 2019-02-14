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
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 81e846c6ffc0361b7154c962dbc047b9e5ae55e7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872979"
---
# <a name="face-detection-with-computer-vision"></a>使用计算机视觉进行人脸检测

计算机视觉检测图片中的人脸，并为每个检测到的脸部生成年龄、性别和人脸边框。 计算机视觉提供可以在[人脸](/azure/cognitive-services/face/)中发现的部分功能。可以使用人脸服务进行更详细的分析，例如人脸识别和姿势检测。  

## <a name="face-detection-examples"></a>人脸检测示例

第一个示例演示了计算机视觉为包含单个人脸的图像返回的 JSON 响应。

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

了解关于[检测特定于域的内容](concept-detecting-domain-content.md)的概念。
