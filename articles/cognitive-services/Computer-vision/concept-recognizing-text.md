---
title: 打印的手写文本识别-计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 识别图像中的印刷文本和手写文本相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365461"
---
# <a name="recognize-printed-and-handwritten-text"></a>识别打印文本和手写文本

计算机视觉提供了许多服务来检测和提取图像中显示的打印文本或手写文本。 这在各种情况下非常有用，例如，注释拍摄、医学记录、安全和银行。 以下三个部分详细介绍三个不同的文本识别 Api，每个 Api 都针对不同用例进行了优化。

## <a name="read-api"></a>读取 API

读取 API 使用最新的识别模型检测图像中的文本内容，并将标识的文本转换为计算机可读的字符流。 它针对文本繁重图像（如已进行数字扫描的文档）和图像（有大量视觉干扰）进行了优化。 它将确定要将哪种识别模型用于文本的每一行，同时支持同时包含打印文本和手写文本的图像。 读取 API 是异步执行的，因为较大的文档可能需要几分钟才能返回结果。

读取操作维护输出中被识别的单词的原始行组。 每一行都包含边界框坐标，行中的每个单词也有其自己的坐标。 如果某个词被自信地识别，则该信息也会传达。 有关详细信息，请参阅[读取 api 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)和[读取 api 预览参考文档](https://go.microsoft.com/fwlink/?linkid=2118322)。

> [!NOTE]
> 此功能仅适用于英语和西班牙语（预览版）文本。

### <a name="image-requirements"></a>图像要求

读取 API 使用满足以下要求的映像：

- 必须以 JPEG、PNG、BMP、PDF 或 TIFF 格式呈现图像。
- 图像的尺寸必须介于 50 x 50 和 10000 x 10000 像素之间。 PDF 页面必须为 17 x 17 英寸或更小。
- 映像的文件大小必须小于20兆字节（MB）。

### <a name="limitations"></a>限制

如果你使用的是免费层订阅，则读取 API 只会处理 PDF 或 TIFF 文档的前两页。 对于付费订阅，它将最多处理200页。 另请注意，该 API 每页最多可检测300行。

## <a name="ocr-optical-character-recognition-api"></a>OCR （光学字符识别） API

计算机视觉的光学字符识别（OCR） API 类似于读取 API，但它是同步执行的，并且未针对大文档进行优化。 它使用较早的识别模型，但适用于更多语言;有关支持的语言的完整列表，请参阅[语言支持](language-support.md#text-recognition)。

如有必要，OCR 会更正已识别文本的旋转，方法是以度为单位返回有关水平图像轴的旋转偏移量。 OCR 还提供每个单词的帧坐标，如下图所示。

![要旋转的图像及其正在读取和分隔的文本](./Images/vision-overview-ocr.png)

有关详细信息，请参阅[OCR 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)。

### <a name="image-requirements"></a>图像要求

OCR API 适用于满足以下要求的映像：

* 必须以 JPEG、PNG、GIF 或 BMP 格式呈现图像。
* 输入图像的大小必须介于 50 x 50 和 4200 x 4200 像素之间。
* 图像中的文本可以旋转 90 度的任何倍数再加最多 40 度的小角度。

### <a name="limitations"></a>限制

在以文本为主的照片上，误报可能来自部分识别的字词。 在某些照片上，尤其是没有任何文本的照片，精度取决于映像类型。

## <a name="recognize-text-api"></a>识别文本 API

> [!NOTE]
> 识别文本 API 将被弃用，以支持读取 API。 读取 API 具有类似的功能，并且已更新为处理 PDF、TIFF 和多页面文件。

识别文本 API 类似于 OCR，但它以异步方式执行，并使用更新的识别模型。 有关详细信息，请参阅[识别文本 API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)。

### <a name="image-requirements"></a>图像要求

识别文本 API 使用满足以下要求的映像：

- 必须以 JPEG、PNG 或 BMP 格式呈现图像。
- 图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间。
- 映像的文件大小必须小于 4 mb。

## <a name="limitations"></a>限制

文本识别操作的准确性取决于图像的质量。 以下因素可能会导致不准确的读取：

* 模糊的图像。
* 手写文本或草体文本。
* 艺术字体样式。
* 文本太小。
* 文本上的复杂背景、阴影、炫光或透视变形。
* 单词开头有超大大写字母或缺少大写字母。
* 下标、上标或删除线文本。

## <a name="next-steps"></a>后续步骤

按照[提取文本（阅读）](./QuickStarts/CSharp-hand-text.md)快速入门，在简单C#的应用中实现文本识别。
