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
ms.openlocfilehash: 5d0a9771e5b999028996676ea72f8def3c5d63cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589850"
---
# <a name="recognize-printed-and-handwritten-text"></a>识别打印文本和手写文本

计算机视觉提供多个服务用于检测和提取图像中显示的打印文本或手写文本。 在笔录、医疗记录、保安和银行等场景中，这些服务非常有用。 以下三个部分详细介绍了针对不同用例优化的三个不同文本识别 API。

## <a name="read-api"></a>读取 API

读取 API 使用我们最新的识别模型检测图像中的文本内容，并将已识别的文本转换为机器可读的字符流。 该 API 已针对包含大量文本的图像（例如，数码扫描的文档）以及包含大量视觉噪点的图像进行优化。 它将确定用于每行文本的识别模型，并支持包含印刷文本和手写文本的图像。 读取 API 以异步方式执行，因为处理较大文档时，可能需要花费好几分钟才能返回结果。

“读取”操作会在其输出中保留已识别字的原始行分组。 每一行附带边框坐标，行中的每个字也有其自身的坐标。 如果某个字的识别置信度较低，该结果中也会反映该信息。 若要了解详细信息，请参阅[读取 api v2.0 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)或[读取 api 3.0 参考文档](https://aka.ms/computer-vision-v3-ref)。

读取操作可以识别英语、西班牙语、德语、法语、意大利语、葡萄牙语和荷兰语中的文本。

### <a name="image-requirements"></a>图像要求

读取 API 可以处理符合以下要求的图像：

- 图像必须以 JPEG、PNG、BMP、PDF 或 TIFF 格式显示。
- 图像的尺寸必须介于 50 x 50 和 10000 x 10000 像素之间。 PDF 页面必须为 17 x 17 英寸或更小。
- 图像的文件大小必须小于 20 MB。

### <a name="limitations"></a>限制

如果使用的是免费层订阅，读取 API 只会处理 PDF 或 TIFF 文档的前两个页面。 使用付费订阅时，它最多可以处理 200 个页面。 另请注意，该 API 最多检测每个页面中的 300 行。

## <a name="ocr-optical-character-recognition-api"></a>OCR（光学字符识别）API

计算机视觉的光学字符识别 (OCR) API 类似于读取 API，但它以同步方式执行，且未针对大型文档进行优化。 该 API 使用早期的识别模型，但可以处理更多的语言；如需所支持的语言的完整列表，请参阅[语言支持](language-support.md#text-recognition)。

如有必要，OCR 会更正已识别文本的旋转，方法是以度为单位返回有关水平图像轴的旋转偏移量。 OCR 还提供每个字的帧坐标，如下图所示。

![正在旋转的图像及其正在读取和画出的文本](./Images/vision-overview-ocr.png)

有关详细信息，请参阅 [OCR 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)。

### <a name="image-requirements"></a>图像要求

OCR API 可以处理符合以下要求的图像：

* 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示。
* 输入图像的大小必须介于 50 x 50 和 4200 x 4200 像素之间。
* 图像中的文本可以旋转 90 度的任何倍数再加最多 40 度的小角度。

### <a name="limitations"></a>限制

在以文本为主的照片上，误报可能来自部分识别的字词。 在某些照片上，尤其是在不包含任何文本的照片上，精度因图像的类型而异。

## <a name="recognize-text-api"></a>识别文本 API

> [!NOTE]
> 随着读取 API 的推出，识别文本 API 已弃用。 读取 API 具有类似的功能，经更新后可以处理 PDF、TIFF 和多页文件。

识别文本 API 类似于 OCR，但它以异步方式执行，并使用更新的识别模型。 有关详细信息，请参阅[识别文本 API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)。

### <a name="image-requirements"></a>图像要求

识别文本 API 可以处理符合以下要求的图像：

- 图像必须以 JPEG、PNG 或 BMP 格式显示。
- 图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间。
- 图像的文件大小必须小于 4 MB。

## <a name="limitations"></a>限制

文本识别操作的准确度取决于图像的质量。 以下因素可能导致读取结果不准确：

* 模糊的图像。
* 手写文本或草体文本。
* 艺术字体样式。
* 文本太小。
* 文本上的复杂背景、阴影、炫光或透视变形。
* 文本过长，或单词的开头缺少大写字母。
* 下标、上标或删除线文本。

## <a name="next-steps"></a>后续步骤

按照[提取文本（阅读）](./QuickStarts/CSharp-hand-text.md)快速入门，在简单的 c # 应用程序中实现文本识别。
