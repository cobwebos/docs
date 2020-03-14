---
title: 语言支持-计算机视觉
titleSuffix: Azure Cognitive Services
description: 本文提供计算机视觉功能支持的自然语言列表;OCR、识别文本和读取。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220134"
---
# <a name="language-support-for-computer-vision"></a>计算机视觉的语言支持

计算机视觉的某些功能支持多种语言;此处未提及的任何功能仅支持英语。

## <a name="text-recognition"></a>文本识别

计算机视觉可以识别多种语言的文本。 具体而言， [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 支持多种语言，而[读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)api 和[识别文本](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)API 只支持英语。 有关此功能以及每个 API 的优点的详细信息，请参阅[识别打印文本和手写文本](concept-recognizing-text.md)。

OCR 会自动检测输入材料的语言，因此无需在 API 调用中指定语言代码。 但是，语言代码总是作为 JSON 响应中 `"language"` 节点的值返回。

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
|塞尔维亚语（西里尔） | `sr-Cyrl` |✔ |
|塞尔维亚语（拉丁） | `sr-Latn` |✔ |
|斯洛伐克语 | `sk` |✔ |
|西班牙语 | `es` |✔ |
|瑞典语 | `sw` |✔ |
|土耳其语 | `tr` |✔ |

## <a name="image-analysis"></a>图像分析

[分析映像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 的一些操作可以返回其他语言的结果，这些结果用 `language` 查询参数指定。 其他操作会以英文形式返回结果，而不考虑所指定的语言，其他操作将对不受支持的语言引发异常。 操作与 `visualFeatures` 和 `details` 查询参数一起指定;有关可以使用图像分析执行的所有操作的列表，请参阅[概述](home.md)。

|语言 | 语言代码 | 类别 | Tags | 说明 | 成人 | 品牌 | Color | 面 | ImageType | 对象 | 名人 | 特征点 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英语 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日语 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙语 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙语 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>后续步骤

开始使用本指南中所述的计算机视觉功能。

* [分析本地映像（REST）](./quickstarts/csharp-analyze.md)
* [提取打印文本（REST）](./quickstarts/csharp-print-text.md)