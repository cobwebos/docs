---
title: 使用 OCR 提取文本 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 通过光学字符识别 (OCR) 提取文本相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0f43724218994818908e87834ed1b70f4bca330b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873796"
---
# <a name="extracting-text-with-optical-character-recognition"></a>使用光学字符识别提取文本

计算机视觉中的光学字符识别 (OCR) 技术可检测图像中的文本内容，并将已识别的文本提取到计算机可读的字符流中。 你可以将结果用于搜索和其他多个目的，如医疗记录、安全和银行。 它会自动检测语言。 OCR 可以节省时间，允许用户拍摄文本而非转录文本，从而为用户提供方便。

OCR 支持 25 种语言。 这些语言包括：阿拉伯语、简体中文、繁体中文、捷克语、丹麦语、荷兰语、英语、芬兰语、法语、德语、希腊语、匈牙利语、意大利语、日语、韩语、挪威语、波兰语、葡萄牙语、罗马尼亚语、俄语、塞尔维亚语(西里尔文和拉丁语)、斯洛伐克语、西班牙语、瑞典语和土耳其语。

如果需要，OCR 会围绕图像水平轴更正所识别文本的方向（以度为单位）。 OCR 提供每个字词的帧坐标，如下图所示。

![一个示意图，描绘正在旋转的图像及其正在读取和画出的文本](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>OCR 要求

计算机视觉可以使用 OCR 从符合以下要求的图像中提取文本：

* 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示
* 输入图像的大小必须介于 50 x 50 和 4200 x 4200 像素之间


输入图像可以旋转 90 度的任何倍数再加最多 40 度的小角度。

## <a name="improving-ocr-accuracy"></a>提高 OCR 准确性

文本识别的准确性取决于图像的质量。 以下情况可能会导致读数不准确：

* 模糊的图像。
* 手写文本或草体文本。
* 艺术字体样式。
* 文本太小。
* 文本上的复杂背景、阴影、炫光或透视变形。
* 单词开头的大写字母过大或缺失
* 下标、上标或删除线文本。

### <a name="ocr-limitations"></a>OCR 限制

在以文本为主的照片上，误报可能来自部分识别的字词。 在某些照片上，尤其在不带任何文本的照片上，精度会因图像类型而存在较大差异。

## <a name="next-steps"></a>后续步骤

了解有关[识别打印文本和手写文本](concept-recognizing-text.md)的概念。
