---
title: 图像审查 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用内容审查器的计算机辅助图像审核和用户-循环查看工具来对成人和猥亵内容进行中等图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: dd6228ea50968c98c5ba151b8af9a0c2fa829582
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684064"
---
# <a name="learn-image-moderation-concepts"></a>了解图像审查概念

使用内容审查器的计算机辅助图像裁决和[查看工具](Review-Tool-User-Guide/human-in-the-loop.md)来对成人和猥亵内容使用中等图像。 扫描图像以查找文本内容并提取该文本，以及检测人脸。 可以将图像与自定义列表进行匹配，并执行进一步操作。

## <a name="evaluating-for-adult-and-racy-content"></a>评估成人和猥亵内容

**评估**操作返回 0 到 1 之间的置信度分数。 它还返回等于 true 或 false 的布尔数据。 这些值可预测图像是否包含潜在的成人或猥亵内容。 使用图像（文件或 URL）调用 API 时，返回的响应包含以下信息：

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` 表示可能有图像在某些情况下被视为有露骨色情内容或成人内容。
> - `isImageRacyClassified` 表示可能有图像在某些情况下被视为有性暗示内容或成人内容。
> - 这些分数介于 0 和 1 之间。 评分越高，模型预测类别可能适用的可能性越高。 此预览版依赖于统计模型，而不是人工编码结果。 我们建议你对自己的内容进行测试，以确定每个类别是否符合要求。
> - 布尔值为 true 或 false，具体情况取决于内部分数阈值。 客户应评估是使用该值，还是根据他们的内容策略确定自定义阈值。

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>使用光学字符识别 (OCR) 检测文本

**光学字符识别 (OCR)** 操作可预测图像中是否存在文本内容，并将其提取出来以进行文本审查，以及用于其他用途。 你可以指定语言。 如果未指定语言，则检测默认为英语。

响应包含以下信息：
- 原始文本。
- 检测到的文本元素及其置信度分数。

示例提取：

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>检测人脸

检测人脸有助于检测个人数据，例如图像中的人脸。 可以检测每个图像中的潜在人脸和潜在人脸的数量。

响应包括以下信息：

- 人脸计数
- 检测到的人脸的位置列表

示例提取：


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>创建和管理自定义列表

在许多在线社区中，在用户上传图像或其他类型的内容之后，冒犯性项目可能在接下来的几天、几周和几个月内多次共享。 从多个地方重复扫描和筛选出相同图像或甚至略微修改的图像版本的成本可能是昂贵的，并且容易出错。

可以将令人反感的图像添加到阻止内容的自定义列表中，而不是多次审核同一图像。 这样，内容审核系统就会将传入图像与自定义列表进行比较，并停止任何进一步处理。

> [!NOTE]
> 最多只能使用 5 个图像列表  ，每个列表中的图像数不得超过 10,000 张  。
>

内容审查器提供了完整的[图像列表管理 API](try-image-list-api.md)，其中包含用于管理自定义图像列表的操作。 从[图像列表 API 控制台](try-image-list-api.md)开始，使用 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[图像列表 .NET 快速入门](image-lists-quickstart-dotnet.md)。

## <a name="matching-against-your-custom-lists"></a>与自定义列表进行匹配

匹配操作允许将传入图像与使用列表操作创建和管理的任何自定义列表进行模糊匹配。

如果找到匹配项，该操作将返回匹配图像的标识符和审查标记。 响应包括以下信息：

- 匹配分数（介于 0 和 1 之间）
- 匹配图像
- 图像标记（在之前的审查期间分配）
- 图像标签

示例提取：

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="review-tool"></a>审阅工具

对于更微妙的情况，请使用内容审查器[审阅工具](Review-Tool-User-Guide/human-in-the-loop.md)及其 API，为你的人员审查人员查看审核结果和内容。 他们检查机器分配的标记并确认其最终决定。

![供人工审查方审阅的图像](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>后续步骤

试用[图像审查 API 控制台](try-image-api.md)并使用 REST API 代码示例。 另请参阅[评审、工作流和作业](./review-api.md)，了解如何设置人为审阅。
