---
title: 语言支持 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 本文提供计算机视觉功能支持的自然语言列表;OCR，图像分析。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 649f1e6cf79aa17d053cc547debf5232120bb4fe
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760628"
---
# <a name="language-support-for-computer-vision"></a>计算机视觉的语言支持

计算机视觉的某些功能支持多种语言；此处未提及的任何功能均只支持英语。

## <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)

计算机视觉的 OCR Api 支持多种语言。 它们不需要指定语言代码。 有关详细信息，请参阅[光学字符识别 (OCR) ](concept-recognizing-text.md) 。

|语言| 语言代码 | OCR API | 读取3.0 版 | 读取3.1 公共预览版 |
|:-----|:----:|:-----:|:---:|:---:|
|阿拉伯语 | `ar`|✔ | | |
|中文(简体) | `zh-Hans`|✔ | |✔ |
|中文(繁体) | `zh-Hant`|✔ | | |
|捷克语 | `cs` |✔ | | |
|丹麦语 | `da` |✔ | | |
|荷兰语 | `nl` |✔ |✔ |✔ |
|英语 | `en` |✔ |✔ |✔ |
|芬兰语 | `fi` |✔ | | |
|法语 | `fr` |✔ |✔ |✔ |
|德语 | `de` |✔ |✔ |✔ |
|希腊语 | `el` |✔ | | |
|匈牙利语 | `hu` |✔ | | |
|意大利语 | `it` |✔ |✔ |✔ |
|日语 | `ja` |✔ | | |
|韩语 | `ko` |✔ | | |
|挪威语 | `nb` |✔ | | |
|波兰语 | `pl` |✔ | | |
|葡萄牙语 | `pt` |✔ |✔ |✔ |
|罗马尼亚语 | `ro` |✔ | | |
|俄语 | `ru` |✔ | | |
|塞尔维亚语（西里尔） | `sr-Cyrl` |✔ | | |
|塞尔维亚语（拉丁） | `sr-Latn` |✔ | | |
|斯洛伐克语 | `sk` |✔ | | |
|西班牙语 | `es` |✔ |✔ |✔ |
|瑞典语 | `sw` |✔ | | |
|土耳其语 | `tr` |✔ | | |

## <a name="image-analysis"></a>图像分析

[分析 - 图像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的某些操作可以返回其他语言（使用 `language` 查询参数指定）的结果。 某些操作会返回英语结果而不管你指定何种语言，另外一些操作会针对不支持的语言引发异常。 操作是通过 `visualFeatures` 和 `details` 查询参数指定的；请参阅[概述](home.md)以获取能够通过图像分析完成的所有操作的列表。

|语言 | 语言代码 | 类别 | 标记 | 说明 | 成人 | 品牌 | Color | 面 | ImageType | 对象 | 名人 | 特征点 |
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
