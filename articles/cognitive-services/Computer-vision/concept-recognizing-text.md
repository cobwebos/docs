---
title: 光学字符识别（OCR）-计算机视觉
titleSuffix: Azure Cognitive Services
description: 与使用计算机视觉 API 通过打印文本和手写文本打印的图像和文档中的光学字符识别（OCR）相关的概念。
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: d1c642a660b24cfc54c9c4308b8956582e13d50a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954734"
---
# <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)

计算机视觉包括新的基于深度学习的光学字符识别（OCR）功能，这些功能可从图像和 PDF 文档中提取打印文本或手写文本。 计算机视觉从模拟文档（图像、扫描的文档）和数字化的文档中提取文本。 你可以从图像中提取文本（例如，具有序列号的许可印版照片或包含序列号的容器），也可以从文档-发票、帐单、财务报表、文章等。 此 OCR 功能作为云中或本地（容器）的托管服务的一部分提供。 此外，它还支持虚拟网络和专用终结点，以满足企业级符合性和隐私需求。

## <a name="read-api"></a>读取 API 

计算机视觉的[读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)是 Microsoft 的最新 OCR 技术，可从图像和多页面 PDF 文档中提取打印文本（七种语言）、手写文本（仅英语）、数字和货币符号。 它经过优化，可以从通配符文本和混合语言的多页面 PDF 文档中提取文本。 它支持在同一图像或文档中检测打印文本和手写文本（仅英语）。 有关支持的语言的完整列表，请参阅计算机视觉页面的[语言支持](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)。

### <a name="how-it-works"></a>工作原理

[读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)支持最大为2000页的文本繁重的文档，因此以异步方式执行。 第一步是调用读取操作。 读取操作采用图像或 PDF 文档作为输入，并返回操作 ID。 

第二步是调用[获取结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750)操作。 此操作采用读取操作创建的操作 ID。 然后，它将从图像或文档中以 JSON 格式返回提取的文本内容。 JSON 响应维护已识别单词的原始行组。 它包括提取的文本行及其边界框坐标。 每个文本行都包含带有坐标和置信度分数的所有已提取单词。

如有必要，请阅读通过返回相对于水平图像轴的旋转偏移量来更正已识别页面的旋转，如下图所示。

![正在旋转的图像及其正在读取和画出的文本](./Images/vision-overview-ocr-read.png)

按照 "[提取打印文本和手写文本](./QuickStarts/CSharp-hand-text.md)" 快速入门，使用 c # 和 REST API 实现 OCR。

### <a name="input-requirements"></a>输入要求

读取 API 采用以下输入：
* 支持的文件格式： JPEG、PNG、BMP、PDF 和 TIFF
* 对于 PDF 和 TIFF，最多处理2000页。 对于免费层订阅服务器，只处理前两页。
* 文件大小必须小于 50 MB 且至少为 50 x 50 像素到 10000 x 10000 像素。
* PDF 尺寸必须最大为 17 x 17 英寸，对应于法律或 A3 纸张大小和更小的尺寸。


### <a name="text-from-images"></a>图像中的文本

以下读取 API 输出显示了从图像中提取的文本线条和单词，其中包含具有不同角度、颜色和字体的文本

![正在旋转的图像及其正在读取和画出的文本](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>文档中的文本

除了映像，读取 API 还会将 PDF 文档作为输入。

![正在旋转的图像及其正在读取和画出的文本](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>英文手写文本

目前，读取操作支持以纯文本形式提取手写文本。

![正在旋转的图像及其正在读取和画出的文本](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>支持的语言中的打印文本

Read API 支持以英语、西班牙语、德语、法语、意大利语、葡萄牙语和荷兰语语言提取打印文本。 如果你的方案需要支持更多语言，请参阅本文档中的 OCR API 概述。 请参阅所有[支持的语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)的列表

![正在旋转的图像及其正在读取和画出的文本](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>混合语言支持

读取 API 支持其中有多种语言的映像和文档，通常称为混合语言文档。 它通过将文档中的每个文本行归类为检测到的语言，然后提取文本内容来实现此目的。

![正在旋转的图像及其正在读取和画出的文本](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用读取服务的开发人员应了解 Microsoft 针对客户数据的策略。 若要了解详细信息，请参阅[Microsoft 信任中心](https://www.microsoft.com/en-us/trust-center/product-overview)上的认知服务页。

### <a name="deploy-on-premises"></a>本地部署

作为 Docker 容器（预览）还提供了读取功能，使你能够在自己的环境中部署新的 OCR 功能。 容器非常适合用于满足特定的安全性和数据管理要求。 请参阅[如何安装和运行读取容器。](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)使用较旧的识别模式，仅支持图像，并以同步方式执行，并立即返回检测到的文本。 它比读取 API 支持[更多语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)。

## <a name="next-steps"></a>后续步骤

- 了解[Read 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)。
- 按照[提取文本](./QuickStarts/CSharp-hand-text.md)快速入门中的步骤，使用 c #、Java、JavaScript 或 Python 以及 REST API 来实现 OCR。
