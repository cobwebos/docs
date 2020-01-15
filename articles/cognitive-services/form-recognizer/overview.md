---
title: 什么是表单识别器？
titleSuffix: Azure Cognitive Services
description: 通过 Azure 认知服务表单识别器，可以从表单文档中识别和提取键值对和表数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 91ea2b68828ac54d4128a90550e9c60e065b719d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379415"
---
# <a name="what-is-form-recognizer"></a>什么是表单识别器？

Azure 表单识别器是一个认知服务，该服务使用机器学习技术从表单文档中识别和提取文本、键值对和表数据。 它会引入表单中的文本并输出包含原始文件中的关系的结构化数据。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 表单识别器由自定义模型、预生成的收据模型和布局 API 组成。 可以使用 REST API 调用表单识别器模型，以降低复杂性，并将该模型集成到工作流或应用程序中。

表单识别器包括下列服务：
* **自定义模型** - 从表单中提取键值对和表数据。 这些模型都是用你自己的数据训练的，因此是针对你的表单量身定制的。
* **预生成的收据模型** - 使用预生成的模型从美国销售收据中提取数据。
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

## <a name="prebuilt-receipt-model"></a>预生成的回执模型

表单识别器还包括用于读取美国的英语销售收据的模型&mdash;由餐馆、油站、零售店等使用的类型（[示例收据](./media/contoso-receipt-small.png)）。 此模型可提取关键信息，如交易的时间和日期、商家信息、税额和总计等。 此外，预生成的回执模型经过训练，可识别和返回回执中的所有文本。

## <a name="layout-api"></a>布局 API

表单识别器还可以使用高清光学字符识别 (OCR) 来提取文本和表结构（与文本关联的行号和列号）。 

## <a name="where-do-i-start"></a>从哪里开始？

**步骤 1：** 请求访问权限：

表单识别器目前只推出了访问权限受限的预览版。 若要访问预览版，请填写并提交[表单识别器访问请求](https://aka.ms/FormRecognizerRequestAccess)表单。 该表单将请求有关你、你的公司以及要在其中使用表单识别器的方案的信息。

**步骤 2：** 在 Azure 门户中创建表单识别器资源：

获得使用表单识别器所需的访问权限时，你会收到一封欢迎电子邮件，其中包含多个链接和资源。 使用该邮件中的“Azure 门户”链接打开 Azure 门户并创建表单识别器资源。

**步骤 3：** 从表单提取数据：

* 自定义 - 按表单训练模型
  * 在没有标签的情况下训练
    * [快速入门：使用 REST API 和 cURL 训练表单识别器模型并提取表单数据](quickstarts/curl-train-extract.md)
    * [快速入门：使用 REST API 和 Python 训练表单识别器模型并提取表单数据](quickstarts/python-train-extract.md)
  * 使用标签进行训练 
    * [使用示例标记工具通过标签来训练表单识别器模型](quickstarts/label-tool.md)
    * [使用 REST API 和 Python 通过标签训练表单识别器模型](quickstarts/python-labeled-data.md) 
* 预生成的收据 - 从美国销售收据中提取数据
  * [快速入门：使用 cURL 提取回执数据](quickstarts/curl-receipts.md)
  * [快速入门：使用 Python 提取回执数据](quickstarts/python-receipts.md)
* 布局 - 从表单提取文本和表结构
  * [快速入门：使用 Python 提取布局数据](quickstarts/python-layout.md)

我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

**步骤 4：** 查看 REST API：

使用以下 API 训练模型并提取表单中的结构化数据。

|名称 |说明 |
|---|---|
| **训练自定义模型**| 使用相同类型的 5 个表单对新模型进行训练，以便分析你的表单。 将 _useLabelFile_ 参数设置为 `true`，以便使用手动标记的数据进行训练。 |
| **分析表单** |使用自定义模型分析作为流传入的单个文档，以从表单中提取文本、键值对和表。  |
| **分析收据** |分析单个回执文档，以提取关键信息和其他回执文本。|
| **分析布局** |分许表单布局，以便提取文本和表结构。|

请浏览 [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)以了解详细信息。 如果熟悉旧版 API，请参阅[新增功能](./whats-new.md)一文，了解最近的变更。

## <a name="input-requirements"></a>输入要求
### <a name="custom-model"></a>自定义模型

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>预生成的回执模型

回执模型的输入要求略有不同。

* 格式必须为 JPEG、PNG、BMP、PDF（文本或扫描时间）或 TIFF。
* 文件大小必须小于 20 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。 
* PDF 尺寸必须最多为 17 x 17 英寸，对应于 Legal 或 A3 纸张尺寸及更小。
* 对于 PDF 和 TIFF，仅处理前 200 页（对于免费层订阅，仅处理前两页）。

## <a name="data-privacy-and-security"></a>数据隐私和安全性

该服务是根据[联机服务条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)以 Azure 服务[预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)的形式提供的。 与所有认知服务一样，使用表单识别器服务的开发人员应该了解针对客户数据的 Microsoft 政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

完成一个[快速入门](quickstarts/curl-train-extract.md)，开始使用[表单识别器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)。
