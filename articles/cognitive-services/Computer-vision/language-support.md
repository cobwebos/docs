---
title: 语言支持的计算机视觉
titleSuffix: Azure Cognitive Services
description: 支持的计算机视觉功能的自然语言的列表。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759599"
---
# <a name="language-support-for-computer-vision"></a>计算机视觉的语言支持

计算机视觉的某些功能支持多种语言;此处未提及的任何功能仅支持英语。

## <a name="text-recognition"></a>文本识别

计算机视觉可以识别许多语言的文本。 具体而言， [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 支持多种语言，而[读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)API 并[识别文本](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)API 仅支持英语。 请参阅[打印和手写文本识别](concept-recognizing-text.md)有关此功能，并且每个 API 的优点的详细信息。

OCR 会自动检测的语言输入的材料，因此无需在 API 调用中指定的语言代码。 但是，语言代码始终返回的值作为`"language"`JSON 响应中的节点。

|语言| 语言代码 | OCR API |
|:-----|:----:|:-----:|
|阿拉伯语 | `ar`|✔ |
|中文(简体) | `zh-Hans`|✔ |
|中文(繁体) | `zh-Hant`|✔ |
|捷克语 | `cs` |✔ |
|丹麦语 | `da` |✔ |
|荷兰语 | `nl` |✔ |
|英语 | `en` |✔ |
|芬兰语 | `fi` |✔ |
|法语 | `fr` |✔ |
|德语 | `de` |✔ |
|希腊语 | `el` |✔ |
|匈牙利语 | `hu` |✔ |
|意大利语 | `it` |✔ |
|日语 | `ja` |✔ |
|韩语 | `ko` |✔ |
|挪威语 | `nb` |✔ |
|波兰语 | `pl` |✔ |
|葡萄牙语 | `pt` |✔ |
|罗马尼亚语 | `ro` |✔ |
|俄语 | `ru` |✔ |
|塞尔维亚语(西里尔文) | `sr-Cyrl` |✔ |
|塞尔维亚语(拉丁语系) | `sr-Latn` |✔ |
|斯洛伐克语 | `sk` |✔ |
|西班牙语 | `es` |✔ |
|瑞典语 | `sw` |✔ |
|土耳其语 | `tr` |✔ |

## <a name="image-analysis"></a>图像分析

某些操作[分析-图像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 可以在其他语言中，使用指定返回的结果`language`查询参数。 无论指定何种语言，在英语中的其他操作返回的结果和其他人引发不受支持语言的异常。 使用指定的操作`visualFeatures`并`details`查询参数; 请参阅[概述](home.md)有关如何使用图像分析的所有操作的列表。

|语言 | 语言代码 | Categories | 标记 | 描述 | 成人 | 品牌 | 颜色 | 人脸 | ImageType | 对象 | 名人 | 特征点 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英语 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日语 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙语 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙语 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>后续步骤

开始使用本指南中提到的计算机视觉功能。

* [分析本地图像 (REST)](./quickstarts/csharp-analyze.md)
* [提取打印的文本 (REST)](./quickstarts/csharp-print-text.md)