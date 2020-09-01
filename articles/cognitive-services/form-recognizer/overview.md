---
title: 什么是表单识别器？
titleSuffix: Azure Cognitive Services
description: 通过 Azure 认知服务表单识别器，可以从表单文档中识别和提取键值对和表数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 0df61c2ee42d468562efd67a2a66a90a5e4fda53
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723549"
---
# <a name="what-is-form-recognizer"></a>什么是表单识别器？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 表单识别器是一个认知服务，该服务使用机器学习技术从表单文档中识别和提取文本、键值对和表数据。 它会引入表单中的文本并输出包含原始文件中的关系的结构化数据。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 表单识别器由自定义模型、预生成的收据模型和布局 API 组成。 可以使用 REST API 调用表单识别器模型，以降低复杂性，并将该模型集成到工作流或应用程序中。

表单识别器包括下列服务：
* **自定义模型** - 从表单中提取键值对和表数据。 这些模型都是用你自己的数据训练的，因此是针对你的表单量身定制的。
* **预生成模型** - 使用预生成模型从独特的表单类型中提取数据。 目前可用的是用于英语销售收据和名片的预生成模型。
* **布局 API** - 从文档中提取文本和表结构及其边框坐标。

<!-- add diagram -->

## <a name="custom-models"></a>自定义模式

表单识别器自定义模型会使用你自己的数据进行训练，并且你只需 5 个示例输入表单即可开始。 训练的模型可以输出结构化数据，其中包含原始形式的文档中的关系。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

训练自定义模型时，可以使用以下选项：在使用标记数据的情况下进行训练，以及在不使用标记数据的情况下进行训练。

### <a name="train-without-labels"></a>在没有标签的情况下训练

默认情况下，表单识别器使用非监督式学习来了解表单中字段与项之间的布局和关系。 当你提交了输入表单后，算法会按类型聚集表单，发现现有的键和表，以及将值关联到键，并将条目关联到表。 这不要求手动标记数据，也不要求进行繁琐的编码和维护，因此建议你先尝试此方法。

### <a name="train-with-labels"></a>使用标签进行训练

当你使用标记的数据进行训练时，该模型会使用你提供的标记表单进行监督式学习，以便提取感兴趣的值。 这会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。

表单识别器使用[布局 API](#layout-api) 来了解印刷体和手写体文本元素的预期大小和位置。 然后，它使用用户指定的标签来了解文档中的键/值关联。 建议使用五个手动标记的相同类型的表单，这样就可以在训练新模型时入门，并根据需要添加更多标记的数据以提高模型准确性。

## <a name="prebuilt-models"></a>预生成的模型

表单识别器还包括用于独特表单类型的预生成模型。
### <a name="prebuilt-receipt-model"></a>预生成的收据模型
预生成的收据模型用于读取澳大利亚、加拿大、英国、印度和美国的英语销售收据 &mdash; 由餐馆、油站、零售店等使用的类型。 此模型可提取关键信息，如交易的时间和日期、商家信息、税额、明细项目和总计等。 此外，预生成的回执模型经过训练，可识别和返回回执中的所有文本。 

![收据示例](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>预生成的名片模型
通过名片模型，你可以从英语名片中提取人员姓名、职务、地址、电子邮件、公司和电话号码等信息。 

![名片示例](./media/business-card-english.jpg)

## <a name="layout-api"></a>布局 API

表单识别器还可以使用高清光学字符识别 (OCR) 来提取文本和表结构（与文本关联的行号和列号）。

## <a name="get-started"></a>入门

按照快速入门开始从表单中提取数据。 我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

* [客户端库快速入门](./quickstarts/client-library.md)（所有语言，多种场景）
* Web UI 快速入门
  * [在有标签的情况下进行训练 - 示例标签工具](quickstarts/label-tool.md)
* REST 快速入门
  * 训练自定义模型并提取表单数据
    * [在没有标签的情况下进行训练 - cURL](quickstarts/curl-train-extract.md)
    * [在没有标签的情况下进行训练 - Python](quickstarts/python-train-extract.md)
    * [在有标签的情况下进行训练 - Python](quickstarts/python-labeled-data.md)
  * 从美国销售收据中提取数据
    * [提取收据数据 - cURL](quickstarts/curl-receipts.md)
    * [提取收据数据 - Python](quickstarts/python-receipts.md)
  * 从表单中提取文本和表结构
    * [提取布局数据 - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>查看 REST API

使用以下 API 训练模型并提取表单中的结构化数据。

|名称 |说明 |
|---|---|
| **训练自定义模型**| 使用相同类型的 5 个表单对新模型进行训练，以便分析你的表单。 将 _useLabelFile_ 参数设置为 `true`，以便使用手动标记的数据进行训练。 |
| **分析表单** |使用自定义模型分析作为流传入的单个文档，以从表单中提取文本、键值对和表。  |
| **分析收据** |分析单个回执文档，以提取关键信息和其他回执文本。|
| **分析布局** |分许表单布局，以便提取文本和表结构。|

请浏览 [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)以了解详细信息。 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

## <a name="input-requirements"></a>输入要求
### <a name="custom-model"></a>自定义模型

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>预生成

回执模型的输入要求略有不同。

* 格式必须为 JPEG、PNG、PDF（文本或扫描件）或 TIFF。
* 文件大小必须小于 20 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸必须最多为 17 x 17 英寸，对应于 Legal 或 A3 纸张尺寸及更小。
* 对于 PDF 和 TIFF，仅处理前 200 页（对于免费层订阅，仅处理前两页）。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

与所有认知服务一样，使用表单识别器服务的开发人员应该了解针对客户数据的 Microsoft 政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

完成一个[快速入门](quickstarts/curl-train-extract.md)，开始使用[表单识别器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)。