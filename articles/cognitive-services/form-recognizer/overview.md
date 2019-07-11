---
title: 什么是表单识别器？
titleSuffix: Azure Cognitive Services
description: 了解如何使用表单识别器来分析表单和表数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e064faf3017b95cb3a5f3d9b89f178fb7f846766
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592625"
---
# <a name="what-is-form-recognizer"></a>什么是表单识别器？

Azure 表单识别器是一个认知服务，该服务使用机器学习技术从表单文档中识别和提取键值对和表数据。 然后，它会输出包含原始文件中的关系的结构化数据。 可以使用简单的 REST API 调用自定义表单识别器模型，以降低复杂性，并轻松地将该模型集成到工作流或应用程序中。 若要开始使用该服务，只需 5 个已填表单文档或 2 个已填表单，再加上一个类型与输入材料相同的空表单即可。 可以快速获取根据特定内容定制的准确结果，无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。

## <a name="custom-models"></a>自定义模式

表单识别器自定义模型会使用你自己的数据进行训练，并且你只需 5 个示例输入表单即可开始。 当你提交了输入数据后，算法会按类型聚集表单，发现现有的键和表，以及将值关联到键，并将条目关联到表。 然后，它会输出包含原始文件中的关系的结构化数据。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

非监督式学习可让模型了解字段与项之间的布局和关系，而无需手动标记数据，或者进行繁琐的编程和维护。 相比之下，预先训练的机器学习模型需要标准化的数据。 这些模型使用了偏离传统格式（如行业特定的表单）的输入材料，因此不够准确。

## <a name="pre-built-receipt-model"></a>预建的回执模型

表单识别器还包括用于读取销售回执的模型。 此模型可提取关键信息，如交易的时间和日期、商家信息、税额和总计等。 此外，预建的回执模型经过训练，可识别和返回回执中的所有文本。

## <a name="what-it-includes"></a>组成部分

表单识别器以 REST API 的形式提供。 可以创建和训练自定义模型并对其进行评分，也可通过调用这些 API 访问预建的模型。 如果需要，可在本地 Docker 容器中训练和运行自定义模型。

## <a name="input-requirements-custom-model"></a>输入要求（自定义模型）

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>请求访问权限

表单识别器目前只推出了访问权限受限的预览版。 若要访问预览版，请填写并提交[表单识别器访问请求](https://aka.ms/FormRecognizerRequestAccess)表单。 该表单将请求有关你、你的公司以及要使用表单识别器的用户方案的信息。 如果 Azure 认知服务团队已批准你的请求，则你会收到一封电子邮件，其中提供了有关如何访问该服务的说明。

## <a name="where-do-i-start"></a>从哪里开始？

**步骤 1：** 在 Azure 门户中创建表单识别器资源。

**步骤 2：** 请按照快速入门使用 REST API：
* [快速入门：使用 REST API 和 cURL 训练表单识别器模型并提取表单数据](quickstarts/curl-train-extract.md)
* [快速入门：使用 REST API 和 Python 训练表单识别器模型并提取表单数据](quickstarts/python-train-extract.md)
* [快速入门：使用 cURL 提取回执数据](quickstarts/curl-receipts.md)
* [快速入门：使用 Python 提取回执数据](quickstarts/python-receipts.md)

我们建议你在学习该技术时使用免费服务。 请记住，每月的免费页数限于 500。

**步骤 3：** 查看 REST API

使用以下 API 训练和提取表单中的结构化数据。

|||
|---|---|
| 训练模型| 使用相同类型的 5 个表单对新模型进行训练，以便分析你的表单。 或者，使用一个空表单和 2 个已填表单进行训练。  |
| 分析表单 |使用自定义模型分析作为流传入的单个文档，以从表单中提取键值对和表。  |
| 分析回执 |分析单个回执文档，以提取关键信息和其他回执文本。|

请浏览 [REST API 参考文档](https://aka.ms/form-recognizer/api)以了解详细信息。 

## <a name="data-privacy-and-security"></a>数据隐私和安全性

该服务是根据[联机服务条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)以 Azure 服务[预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)的形式提供的。 与所有认知服务一样，使用表单识别器服务的开发人员应该了解针对客户数据的 Microsoft 政策。 请参阅 Microsoft 信任中心上的[“认知服务”页面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)来了解详细信息。

## <a name="next-steps"></a>后续步骤

完成一个[快速入门](quickstarts/curl-train-extract.md)，开始使用[表单识别器 API](https://aka.ms/form-recognizer/api)。
