---
title: 光学字符识别 (OCR) - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 与光学字符识别相关的概念 (使用计算机视觉 API 打印和手写文本的图像和文档的 OCR) 。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cbcfddcd02a3998b3b35b01d386816735c59ae7e
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526402"
---
# <a name="optical-character-recognition-ocr"></a>光学字符识别 (OCR)

Azure 的计算机视觉 API 包含光学字符识别 (OCR) 功能，这些功能可从图像中提取打印文本或手写文本。 你可以从图像中提取文本，如许可证印版照片或带有序列号的容器，以及文档-发票、帐单、财务报表、文章等。

## <a name="read-api"></a>读取 API 

计算机视觉 [读取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 是 Azure 的最新 OCR 技术 ([了解哪些新增功能](./whats-new.md)) ，该功能提取了几种语言的打印文本 () ，手写文本仅限图像和多页面 PDF 文档中 (、数字和货币符号。 它经过优化，可以从带有混合语言的文本繁重图像和多页面 PDF 文档中提取文本。 它支持在同一图像或文档中检测打印文本和手写文本。

![OCR 如何将图像和文档转换为带有提取文本的结构化输出](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>输入要求
**读取**调用采用图像和文档作为其输入。 它们具有以下要求：

* 支持的文件格式：JPEG、PNG、BMP、PDF 和 TIFF
* 对于 PDF 和 TIFF 文件，最多2000个页面只 (处理免费层) 的前两页。
* 对于免费层，文件大小必须小于 50 MB (4 MB) 和至少 50 x 50 像素到 10000 x 10000 像素的维度。 
* PDF 尺寸必须为 17 x 17 英寸或更小，对应于 Legal 或 A3 纸张尺寸及更小。

### <a name="read-31-preview-allows-selecting-pages"></a>阅读3.1 预览版允许选择页 (s) 
使用 [Read 3.1 预览 API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)，对于大的多页文档，你可以提供特定页码或页面范围作为输入参数，以便只从这些页面提取文本。 这是一个新的输入参数，以及可选的语言参数。

> [!NOTE]
> **语言输入** 
>
> [Read 调用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)具有适用于 language 的可选请求参数。 这是文档中文本的 BCP-47 语言代码。 Read 支持自动语言标识和多语言文档，因此，如果想要强制将文档作为该特定语言进行处理，只需提供语言代码。

## <a name="the-read-call"></a>Read 调用

读取 API 的 [读取调用](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 采用图像或 PDF 文档作为输入，并以异步方式提取文本。 调用返回并带有一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的操作 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [计算机视觉定价](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)页面包含用于读取的定价层。 每个分析的图像或页面都是一个事务。 如果使用包含100页的 PDF 或 TIFF 文档调用该操作，则读取操作会将其计为100个事务，并将为100个交易计费。 如果对操作发出了50个调用，并且每次调用提交了包含100页的文档，则会向你收取 50 X 100 = 5000 事务的费用。

## <a name="the-get-read-results-call"></a>获取读取结果调用

第二步是调用 [获取读取结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) 操作。 此操作使用读取操作创建的操作 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 此操作以迭代方式调用，直到它返回 **成功** 值。 使用1到2秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|状态 | 字符串 | notStarted：操作尚未开始。 |
| |  | 正在运行：正在处理操作。 |
| |  | failed：操作失败。 |
| |  | succeeded：操作成功。 |

> [!NOTE]
> 免费层将请求速率限制为每分钟20个调用。 付费层允许每秒10个请求 (RPS) ，可以根据请求增加。 使用 Azure 支持渠道或你的帐户团队 (RPS) 速率请求一个更高的请求。

当 " **状态** " 字段的值为 " **成功** " 时，JSON 响应包含图像或文档中提取的文本内容。 JSON 响应会维护已识别单词的原始分组。 其中包括提取的文本行及其边界框坐标。 每个文本行都包含所有提取的单词及其坐标和可信度分数。

## <a name="sample-json-output"></a>示例 JSON 输出

请参阅以下成功的 JSON 响应示例：

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
### <a name="read-31-preview-adds-text-line-style-latin-languages-only"></a>阅读 3.1 preview (仅限拉丁语言添加文本线条样式) 
[Read 3.1 预览 API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)输出**外观**对象，分类每个文本行是打印还是手写样式以及置信度分数。 此功能仅支持拉丁语。

```json
  "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.836
            }
```
开始 [计算机视觉 OCR SDK 快速入门](./quickstarts-sdk/client-library.md) 和 [读取 REST API 快速入门](./QuickStarts/CSharp-hand-text.md) ，开始将 OCR 功能集成到应用程序中。

## <a name="supported-languages-for-print-text"></a>打印文本支持的语言
[Read 3.0 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)支持以英语、西班牙语、德语、法语、意大利语、葡萄牙语和荷兰语语言提取打印文本。

有关 OCR 支持的语言的完整列表，请参阅 [支持的语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 。

### <a name="read-31-preview-adds-simplified-chinese-and-japanese"></a>阅读3.1 预览版添加简体中文和日语
[Read 3.1 API 公共预览版](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)增加了对简体中文和日语的支持。 如果你的方案需要支持更多语言，请参阅 [OCR API](#ocr-api) 部分。 

## <a name="supported-languages-for-handwritten-text"></a>手写文本支持的语言
读取操作当前支持纯文本形式提取手写文本。

## <a name="use-the-rest-api-and-sdk"></a>使用 REST API 和 SDK
对于大多数客户而言， [读取 REST API](./QuickStarts/CSharp-hand-text.md) 是首选选项，因为它易于集成，并可实现快速工作效率。 当你集中精力满足客户需求时，Azure 和计算机视觉服务可处理规模、性能、数据安全和合规性需求。

## <a name="deploy-on-premise-with-docker-containers"></a>在本地部署 Docker 容器
[Read 2.0 Docker 容器 (预览版) ](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)使你能够在自己的本地环境中部署新的 OCR 功能。 容器非常适合用于满足特定的安全性和数据管理要求。

## <a name="example-outputs"></a>示例输出

### <a name="text-from-images"></a>图像中的文本

以下读取 API 输出显示了从具有不同文本角度、颜色和字体的图像提取的文本。

![以不同的颜色和角度显示的多个字词的图像，其中列出了提取的文本](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>文档中的文本

读取 API 还可以采用 PDF 文档作为输入。

![已列出提取文本的发票文档](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>手写文本

读取操作从图像中提取手写文本 (当前仅用英文) 。

![已列出提取文本的手写便笺的图像](./Images/handwritten-example.png)

### <a name="printed-text"></a>打印的文本

读取操作可以提取多种不同语言的打印文本。

![已列出提取文本的西班牙语教科书的图像](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>混合语言文档

读取 API 支持包含多种不同语言的映像和文档，通常称为混合语言文档。 它的工作方式是将文档中的每个文本行分类为检测到的语言，然后提取其文本内容。

![几种语言的短语的图像，其中列出了提取的文本](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 使用较旧的识别模式，仅支持图像，且以同步方式执行，并立即返回检测到的文本。 请参阅 [OCR 支持的语言](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) ，然后阅读 API。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用读取/OCR 服务的开发人员应了解 Microsoft 针对客户数据的策略。 有关详细信息，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trust-center/product-overview)中的“认知服务”页。

> [!NOTE]
> 计算机 Vison 2.0 RecognizeText 操作被弃用，以支持本文涵盖的新读取 API。 现有客户应 [使用读取操作转换为](upgrade-api-versions.md)。

## <a name="next-steps"></a>后续步骤

- 开始计算机视觉在 c #、Java、JavaScript 或 Python 中 [阅读 3.0 SDK 快速入门](./quickstarts-sdk/client-library.md) 。
- 使用 c #、Java、JavaScript 或 Python 中的 [读取 3.0 REST API 快速入门](./QuickStarts/CSharp-hand-text.md) ，了解如何使用 REST api。
- 了解[读取 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)。
- 了解 [Read 3.1 公开预览版 REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) 增加了简体中文和日语支持。
