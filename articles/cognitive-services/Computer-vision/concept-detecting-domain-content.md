---
title: 检测特定于域的内容 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 描述图像的相关概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: a9c71fa7e5d86cfeb4fe6fab44bbce241546ccb8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342546"
---
# <a name="detecting-domain-specific-content"></a>检测特定领域的内容

除了标记和顶级分类以外，计算机视觉还支持专用（或特定于域的）信息。 专用信息可作为独立的方法实现或与高级分类一起实现。 该信息用作通过添加特定于域的模型进一步优化 86 类别分类的方法。

使用特定于域的模型有以下两个选项：

* 作用域分析  
  通过调用 HTTP POST 调用仅分析所选模型。 如果知道要使用的模型，请指定该模型的名称。 仅获取与该模型相关的信息。 例如，可以使用此选项仅查找名人识别。 响应包含可能匹配的名人列表及其置信度分数。
* 增强分析  
  分析以提供与 86 类别分类中的类别相关的其他详细信息。 当除了一个或多个特定于域的模型中的详细信息以外，用户还希望获取常规图像分析时，可应用此选项。 当调用此方法时，首先会调用 86 类别分类的分类器。 如果任何类别与已知或匹配模型的类别相匹配，将执行第二轮分类器调用。 例如，如果 HTTP POST 调用的 `details` 参数设置为“all”或包括“celebrities”，则在调用 86 类别分类器后，该方法将调用名人分类器。 如果图像被分类为 `people_` 或该类别的子类别，则调用名人分类器。

## <a name="listing-domain-specific-models"></a>列出特定于域的模型

可以列出计算机视觉支持的特定于域的模型。 目前，计算机视觉支持以下特定于域的模型，用于检测特定于域的内容：

| 名称 | Description |
|------|-------------|
| 名人 | 名人识别，支持属于 `people_` 类别的图像 |
| 地标 | 地标识别，支持属于 `outdoor_` 或 `building_` 类别的图像 |

### <a name="domain-model-list-example"></a>域模型列表示例

以下 JSON 响应列出了计算机视觉支持的特定于域的模型。

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

了解有关[对图像进行分类](concept-categorizing-images.md)的概念。