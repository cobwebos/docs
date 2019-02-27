---
title: 使用光学字符识别 (OCR) 提取文本 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 通过光学字符识别 (OCR) 提取文本相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310434"
---
# <a name="extract-text-with-optical-character-recognition"></a>使用光学字符识别提取文本

计算机视觉的光学字符识别 (OCR) 功能可检测图像中的文本内容，并将已识别的文本转换为计算机可读的字符流。 你可以将结果用于多个目的，例如搜索、医疗记录、安全和银行。 

OCR 支持 25 种语言：阿拉伯语、简体中文、繁体中文、捷克语、丹麦语、荷兰语、英语、芬兰语、法语、德语、希腊语、匈牙利语、意大利语、日语、韩语、挪威语、波兰语、葡萄牙语、罗马尼亚语、俄语、塞尔维亚语(西里尔文和拉丁语)、斯洛伐克语、西班牙语、瑞典语和土耳其语。 OCR 会自动检测所检测到的文本的语言。

如有必要，OCR 会更正已识别文本的旋转，方法是以度为单位返回有关水平图像轴的旋转偏移量。 OCR 还提供每个字词的帧坐标，如下图所示。

![一个示意图，描绘正在旋转的图像及其正在读取和画出的文本](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>映像要求

计算机视觉可以使用 OCR 从符合以下要求的图像中提取文本：

* 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示
* 输入图像的大小必须介于 50 x 50 和 4200 x 4200 像素之间
* 图像中的文本可以旋转 90 度的任何倍数再加最多 40 度的小角度。

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

在以文本为主的图像上，误报可能来自部分识别的字词。 在某些图像上，尤其是在不带任何文本的照片上，精度会因图像类型而存在较大差异。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [OCR 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)。
