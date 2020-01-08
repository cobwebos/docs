---
title: 如何为自定义模型窗体识别器生成定型数据集
titleSuffix: Azure Cognitive Services
description: 了解如何确保对定型数据集进行优化，以便为窗体识别器模型定型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380620"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>为自定义模型构建定型数据集

使用窗体识别器自定义模型时，需要提供自己的定型数据，以便该模型可以培训特定于行业的窗体。 您可以使用五种填充窗体或空窗体来训练模型（文件名中必须包含单词 "empty"）以及两个填充窗体。 即使您具有足够的填充窗体来进行定型，将空的窗体添加到定型数据集也可以提高模型的准确性。

如果要使用手动标记的定型数据，则应使用至少五个相同类型的窗体开始。 您仍然可以在同一数据集中使用未标记的窗体和空窗体。

## <a name="training-data-tips"></a>训练数据提示

使用针对定型进行优化的数据集很重要。 使用以下提示来确保从[定型自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)操作获得最佳结果：

* 如果可能，请使用基于文本的 PDF 文档而不是基于图像的文档。 扫描的 Pdf 作为图像处理。
* 对于填充窗体，请使用填充了所有字段的示例。
* 在每个字段中使用具有不同值的表格。
* 如果你的表单图像质量较低，请使用较大的数据集（例如，10-15 的图像）。
* 训练数据集的总大小最多可达500页。

## <a name="general-input-requirements"></a>一般输入要求

请确保您的定型数据集也遵循所有窗体识别器内容的输入要求。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>上传定型数据

将用于定型的窗体文档集中在一起后，需要将其上传到 Azure blob 存储容器。 如果你不知道如何使用容器创建 Azure 存储帐户，请遵循[适用于 Azure 门户的 Azure 存储快速入门](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

### <a name="organize-your-data-in-subfolders-optional"></a>组织子文件夹中的数据（可选）

默认情况下，[定型自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)API 将仅使用位于存储容器根目录下的窗体文档。 但是，如果在 API 调用中指定数据，则可以训练子文件夹中的数据。 通常，[定型自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)调用的正文采用以下形式，其中 `<SAS URL>` 是容器的共享访问签名 URL：

```json
{
  "source":"<SAS URL>"
}
```

如果将以下内容添加到请求正文，则 API 将针对位于子文件夹中的文档进行训练。 `"prefix"` 字段是可选的，它会将定型数据集限制为其路径以给定字符串开头的文件。 例如，`"Test"`的值会导致 API 仅查看以 "Test" 开头的文件或文件夹。

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

现在，您已经了解了如何构建定型数据集，接下来请学习快速入门教程来训练自定义窗体识别器模型，并在窗体上开始使用它。

* [快速入门：通过使用曲线来定型模型和提取窗体数据](./quickstarts/curl-train-extract.md)
* [快速入门：通过 Python 对模型进行定型并使用 REST API 提取窗体数据](./quickstarts/python-train-extract.md)
* [使用 REST API 和 Python 为标签定型](./quickstarts/python-labeled-data.md)