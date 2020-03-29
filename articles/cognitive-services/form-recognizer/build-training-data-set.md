---
title: 如何为自定义模型构建训练数据集 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 了解如何确保您的培训数据集经过优化，以训练表单识别器模型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380620"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>为自定义模型构建训练数据集

使用窗体识别器自定义模型时，可以提供您自己的训练数据，以便模型可以培训到特定于行业的窗体。 可以训练模型具有五个填写的表单或一个空窗体（必须在文件名中包含单词"空"）以及两个填写的表单。 即使您有足够的填写表单来训练，向训练数据集添加一个空表单也可以提高模型的准确性。

如果要使用手动标记的训练数据，则应从至少五种相同类型的窗体开始。 您仍然可以在同一数据集中使用未标记的窗体和空窗体。

## <a name="training-data-tips"></a>培训数据提示

使用针对培训优化的数据集非常重要。 使用以下提示确保您从[训练自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)操作中获得最佳结果：

* 如果可能，请使用基于文本的 PDF 文档而不是基于图像的文档。 扫描的 PDF 以图像方式处理。
* 对于填写的表单，请使用已填写其所有字段的示例。
* 在每个字段中使用具有不同值的表格。
* 如果表单图像质量较低，请使用较大的数据集（例如 10-15 个图像）。
* 训练数据集的总大小可达 500 页。

## <a name="general-input-requirements"></a>一般输入要求

确保您的培训数据集也遵循所有表单识别器内容的输入要求。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>上传您的培训数据

将用于培训的表单文档集放在一起后，需要将其上载到 Azure Blob 存储容器。 如果您不知道如何使用容器创建 Azure 存储帐户，请遵循 Azure 门户的[Azure 存储快速入门](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

### <a name="organize-your-data-in-subfolders-optional"></a>在子文件夹中组织数据（可选）

默认情况下，[训练自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)API 将仅使用位于存储容器根目录的表单文档。 但是，如果在 API 调用中指定数据，则可以使用子文件夹中的数据进行训练。 通常，[训练自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)调用的正文具有以下形式，其中容器的共享`<SAS URL>`访问签名 URL 的位置：

```json
{
  "source":"<SAS URL>"
}
```

如果将以下内容添加到请求正文中，API 将使用位于子文件夹中的文档进行训练。 该`"prefix"`字段是可选的，并将训练数据集限制为路径以给定字符串开头的文件。 因此，例如，`"Test"`值将导致 API 仅查看以单词"Test"开头的文件或文件夹。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何构建训练数据集，请按照快速入门来训练自定义表单识别器模型，并开始在窗体上使用它。

* [快速入门：使用 cURL 训练模型并提取表单数据](./quickstarts/curl-train-extract.md)
* [快速入门：使用 PYTHON 的 REST API 训练模型并提取表单数据](./quickstarts/python-train-extract.md)
* [使用 REST API 和 Python 通过标签进行训练](./quickstarts/python-labeled-data.md)