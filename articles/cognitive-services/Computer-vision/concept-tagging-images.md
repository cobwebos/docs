---
title: 内容标记-计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解与计算机视觉 API 的图像标记功能相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244505"
---
# <a name="applying-content-tags-to-images"></a>将内容标记应用于图像

计算机视觉在上千个可识别对象、生物、风景和操作的基础上返回标记。 如果标记含混不清或者不常见，API 响应会提供“提示”，明确已知设置上下文中的标记的含义。 标记未组织为分类，并且不存在继承层次结构。 内容标记的集合构成在完整句子中显示为用户可读语言的图像“说明”的基础。 请注意，此时图像说明仅支持英语。

上传图像或指定图像 URL 后，计算机视觉算法在对象、生物和图像中标识的操作的基础上输出标记。 标记并不局限于主体（如前景中的人员），还包括设置（室内或室外）、家具、工具、工厂、动物、附件、小工具等。

## <a name="image-tagging-example"></a>图像标记示例

以下 JSON 响应表明计算机视觉在示例图像中检测到标记可视功能时所返回的内容。

![一座蓝色的房子和前院](./Images/house_yard.png)。

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

## <a name="use-the-api"></a>使用 API

标记功能是 [分析映像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的一部分。 可以通过本机 SDK 或 REST 调用来调用此 API。 将 `Tags` 包括在 **visualFeatures** 查询参数中。 然后，在获取完整 JSON 响应时，就只需分析 `"tags"` 部分内容的字符串。

* [快速入门：计算机视觉 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入门：分析图像 (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>后续步骤

了解用于对图像进行 [分类](concept-categorizing-images.md) 和 [描述图像](concept-describing-images.md)的相关概念。
