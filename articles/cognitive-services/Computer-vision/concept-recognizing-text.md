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
ms.openlocfilehash: 83e76cd96e09b0e136d2bfbe2e5863b289724bdd
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284245"
---
# <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)

Microsoft 的计算机视觉 API 包含用于从图像和 PDF 文档中提取打印文本或手写文本的光学字符识别（OCR）功能。 OCR Api 从模拟文档（图像、扫描的文档）和数字化的文档中提取文本。 你可以从图像中提取文本（例如，具有序列号的许可印版照片或包含序列号的容器），也可以从文档-发票、帐单、财务报表、文章等。 新的读取 OCR API 作为云中的托管服务或本地（容器）的一部分提供。 此外，它还支持虚拟网络和专用终结点，以满足企业级符合性和隐私需求。

## <a name="read-api"></a>读取 API 

计算机视觉的[读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)是 Microsoft 的最新 OCR 技术，它以多种语言（仅限英语）、数字和货币符号从图像和多页面 PDF 文档提取打印文本。 它经过优化，可以从通配符文本和混合语言的多页面 PDF 文档中提取文本。 它支持在同一图像或文档中检测打印文本和手写文本（仅英语）。 请参阅[OCR 支持的语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)的完整列表页。

### <a name="how-ocr-works"></a>OCR 的工作方式

[读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)支持最大为2000页的文本繁重的文档，因此以异步方式执行。 第一步是调用读取操作。 读取操作采用图像或 PDF 文档作为输入，并返回操作 ID。 

第二步是调用[获取结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750)操作。 此操作采用读取操作创建的操作 ID。 然后，它将从图像或文档中以 JSON 格式返回提取的文本内容。 JSON 响应维护已识别单词的原始行组。 它包括提取的文本行及其边界框坐标。 每个文本行都包含其坐标和置信度分数的所有提取单词。

如有必要，请阅读通过返回相对于水平图像轴的旋转偏移量来更正已识别页面的旋转，如下图所示。

![OCR 如何通过提取的文本将图像和文档转换为结构化输出](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>示例 OCR 输出

成功的响应会以 JSON 格式返回，如以下示例中所示：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

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

Read 3.0 API 支持以英语、西班牙语、德语、法语、意大利语、葡萄牙语和荷兰语语言提取打印文本。 [阅读 3.1-preview. 1 API 公共预览版](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)增加了对简体中文的支持。 如果你的方案需要支持更多语言，请参阅本文档中的 OCR API 概述。 请参阅所有[OCR 支持的语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)的列表

![正在旋转的图像及其正在读取和画出的文本](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>混合语言支持

读取 API 支持其中有多种语言的映像和文档，通常称为混合语言文档。 它通过将文档中的每个文本行归类为检测到的语言，然后提取文本内容来实现此目的。

![正在旋转的图像及其正在读取和画出的文本](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用读取服务的开发人员应了解 Microsoft 针对客户数据的策略。 若要了解详细信息，请参阅[Microsoft 信任中心](https://www.microsoft.com/en-us/trust-center/product-overview)上的认知服务页。

### <a name="containers-for-on-premise-deployment"></a>用于本地部署的容器

作为 Docker 容器（预览）还提供了读取功能，使你能够在自己的环境中部署新的 OCR 功能。 容器非常适合用于满足特定的安全性和数据管理要求。 请参阅[如何安装和运行读取容器。](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)使用较旧的识别模式，仅支持图像，并以同步方式执行，并立即返回检测到的文本。 请参阅[OCR 支持的语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)，而不是读取 API。

## <a name="next-steps"></a>后续步骤

- 了解[Read 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)。
- 了解[Read 3.1-preview. 1 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)增加了简体中文支持。
- 按照[提取文本](./QuickStarts/CSharp-hand-text.md)快速入门中的步骤，使用 c #、Java、JavaScript 或 Python 以及 REST API 来实现 OCR。
