---
title: 检测特定于域的内容 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解如何指定图像分类域以返回有关图像的更多详细信息。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e4b64e00f71768a8821c83a73b019f77089e1b3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368063"
---
# <a name="detect-domain-specific-content"></a>检测特定于域的内容

除标记和高级分类外，计算机视觉还支持使用已经过专门数据训练的模型执行进一步特定于域的分析。

可通过两种方法使用特定于域的模型：使用模型本身（作用域分析）或用作分类功能的增强。

### <a name="scoped-analysis"></a>作用域分析

可通过调用 [Models/\<model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API，仅使用选择的特定于域的模型来分析图像。

以下是 **models/celebrities/analyze** API 为给定图像返回的示例 JSON 响应：

![Satya Nadella 站着、 微笑](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>增强版分类分析

特定于域的模型还可用于对常规图像分析进行补充。 可通过在 [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 调用的 *details* 参数中指定特定于域的模型，作为[高级分类](concept-categorizing-images.md)的一部分执行此操作。

在这种情况下，首先会调用 86 类别分类分类器。 如果检测到的任何类别具有匹配的特定于域的模型，图像也会通过该模型并会添加结果。

以下 JSON 响应展示可如何在更广泛的分类分析中以 `detail` 节点的形式包含特定于域的分析。

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>列出特定于域的模型

目前，计算机视觉支持以下特定于域的模型：

| Name | 描述 |
|------|-------------|
| 名人 | 名人识别，支持属于 `people_` 类别的图像 |
| landmarks | 地标识别，支持属于 `outdoor_` 或 `building_` 类别的图像 |

调用 [Models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API 将返回此信息，以及每个模型可应用于的类别：

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解有关[对图像进行分类](concept-categorizing-images.md)的概念。
