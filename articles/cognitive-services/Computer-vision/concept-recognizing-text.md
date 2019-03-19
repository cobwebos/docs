---
title: 识别印刷文本、手写文本 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 识别图像中的印刷文本和手写文本相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9bb574fcb9782aad41ea0fd276b8addee19caf01
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588969"
---
# <a name="recognize-printed-and-handwritten-text"></a>识别打印文本和手写文本

计算机视觉提供大量服务，检测并提取打印或手写图像中显示的文本。 这可在各种方案，例如便捷性、 医疗记录、 安全性和银行中。 以下三个部分详细三个不同的文本识别 Api，每个针对不同的用例优化。

## <a name="read-api"></a>读取 API

读取 API 检测图像使用我们最新的识别模型中的文本内容，并将识别的文本转换到计算机可识别的字符流。 它经过优化的文字较多映像 （例如已进行数字扫描的文档） 以及对于具有大量视觉干扰的图像。 它以异步方式执行由于较大的文档可能要花几分钟时间才能返回结果。

读取操作维护其输出中的已识别单词的原始行分组。 每一行均与边界框坐标和每个单词的行中也有其自己的坐标。 如果 word 已识别出与低置信度，该信息还传达。 请参阅[阅读 API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)若要了解详细信息。

> [!NOTE]
> 此功能目前处于预览状态和功能仅适用于英语文本。

### <a name="image-requirements"></a>映像要求

读取 API 适用于满足以下要求的映像：

- 必须以 JPEG、 PNG、 BMP、 PDF 或 TIFF 格式显示图像。
- 图像尺寸必须在 50 x 50 到 4200 x 4200 像素之间。 PDF 页必须 17 x 17 英寸或更小。
- 图像的文件大小必须小于 20 兆字节 (MB)。

### <a name="limitations"></a>限制

如果使用免费层订阅时，读取 API 将仅处理 PDF 或 TIFF 文档的前两页。 使用付费订阅，它将处理最多 200 个页面。 另请注意，API 将检测最多 300 每页行数。

## <a name="ocr-optical-character-recognition-api"></a>OCR （光学字符识别） API

计算机视觉的光学字符识别 (OCR) API 类似于阅读 API，但它以同步方式执行，并不适合大型文档。 它使用更多语言使用但工作原理的更早识别模型。

OCR 支持 25 种语言：阿拉伯语、简体中文、繁体中文、捷克语、丹麦语、荷兰语、英语、芬兰语、法语、德语、希腊语、匈牙利语、意大利语、日语、韩语、挪威语、波兰语、葡萄牙语、罗马尼亚语、俄语、塞尔维亚语(西里尔文和拉丁语)、斯洛伐克语、西班牙语、瑞典语和土耳其语。 OCR 会自动检测所检测到的文本的语言。

如有必要，OCR 会更正已识别文本的旋转，方法是以度为单位返回有关水平图像轴的旋转偏移量。 OCR 还提供了每个单词的帧坐标，如下图中所示。

![一个示意图，描绘正在旋转的图像及其正在读取和画出的文本](./Images/vision-overview-ocr.png)

请参阅[OCR 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)若要了解详细信息。

### <a name="image-requirements"></a>映像要求

OCR API 适用于满足以下要求的映像：

* 必须以 JPEG、 PNG、 GIF 或 BMP 格式显示图像。
* 输入图像的大小必须介于 50 x 50 到 4200 x 4200 像素之间。
* 图像中的文本可以旋转 90 度的任何倍数再加最多 40 度的小角度。

### <a name="limitations"></a>限制

在以文本为主的照片上，误报可能来自部分识别的字词。 在一些照片，尤其是不带任何文本的照片上精度而异的图像类型。

## <a name="recognize-text-api"></a>识别文本 API

> [!NOTE]
> 读取 API 支持，识别文本 API 已被弃用。 读取 API 有相似的功能，并将更新，以处理 PDF、 TIFF 和多页文件。

识别文本 API 类似于 OCR，但它以异步方式执行，并使用更新后的识别模型。 请参阅[识别文本 API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)若要了解详细信息。

### <a name="image-requirements"></a>映像要求

识别文本 API 适用于满足以下要求的映像：

- 必须以 JPEG、 PNG 或 BMP 格式显示图像。
- 图像尺寸必须在 50 x 50 到 4200 x 4200 像素之间。
- 图像的文件大小必须小于 4 兆字节 (MB)。

## <a name="improve-results"></a>改进结果

文本识别操作的准确性取决于图像的质量。 以下因素可能会导致读取不准确：

* 模糊的图像。
* 手写文本或草体文本。
* 艺术字体样式。
* 文本太小。
* 文本上的复杂背景、阴影、炫光或透视变形。
* 过大或缺少大写字母开头的单词。
* 下标、上标或删除线文本。

## <a name="next-steps"></a>后续步骤

请按照[提取打印的文本 (OCR)](./quickstarts/csharp-print-text.md)快速入门： 在一个简单实现文本识别C#应用程序。
