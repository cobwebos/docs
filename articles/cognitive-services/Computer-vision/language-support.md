---
title: 语言支持 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 计算机视觉功能支持的自然语言的列表。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 516d21bc69bbc20f924a3bdf39eda7245fc08a28
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882968"
---
# <a name="language-support-for-computer-vision"></a>计算机视觉的语言支持

计算机视觉的某些功能支持多种语言；此处未提及的任何功能均只支持英语。

## <a name="text-recognition"></a>文本识别

计算机视觉可以识别许多语言的文本。 具体说来，[OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 支持多种语言，而[读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API 和[识别文本](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API 仅支持英语。 请参阅[识别印刷文本和手写文本](concept-recognizing-text.md)，详细了解此功能以及每个 API 的优势。

OCR 会自动检测输入材料的语言，因此无需在 API 调用中指定语言代码。 但是，在 JSON 响应中，语言代码始终作为 `"language"` 节点的值返回。

|语言| 语言代码 | OCR API |
|:-----|:----:|:-----:|
|阿拉伯语 | `ar`|✔ |
|简体中文 | `zh-Hans`|✔ |
|繁体中文 | `zh-Hant`|✔ |
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
|塞尔维亚语(西里尔语) | `sr-Cyrl` |✔ |
|塞尔维亚语(拉丁语) | `sr-Latn` |✔ |
|斯洛伐克语 | `sk` |✔ |
|西班牙语 | `es` |✔ |
|瑞典语 | `sw` |✔ |
|土耳其语 | `tr` |✔ |

## <a name="image-analysis"></a>图像分析

[分析 - 图像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的某些操作可以返回其他语言（使用 `language` 查询参数指定）的结果。 某些操作会返回英语结果而不管你指定何种语言，另外一些操作会针对不支持的语言引发异常。 操作是通过 `visualFeatures` 和 `details` 查询参数指定的；请参阅[概述](home.md)以获取能够通过图像分析完成的所有操作的列表。

|语言 | 语言代码 | Categories | Tags | 描述 | 成人 | 品牌 | 颜色 | 人脸 | ImageType | 对象 | 名人 | 特征点 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英语 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日语 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙语 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙语 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>后续步骤

本指南中提及的计算机视觉功能入门。

* [分析本地图像 (REST)](./quickstarts/csharp-analyze.md)
* [提取印刷体文本 (REST)](./quickstarts/csharp-print-text.md)