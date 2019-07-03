---
title: 如何生成用于自定义模型的窗体识别器的定型数据集
titleSuffix: Azure Cognitive Services
description: 了解如何确保你的训练数据集进行了优化窗体识别器模型定型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ad9bba53390e3c4262f999ebcc57ab354f1e3d69
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537617"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>生成用于自定义模型的定型数据集

使用窗体识别器自定义模型，以便于特定于行业的窗体可以训练该模型提供训练数据。 加两个已填写的窗体上，可以对使用五个已填写的窗体或一个空窗体 （必须在文件名中包含单词"空"） 对模型进行定型。 即使有足够已填写的窗体进行定型，将一个空窗体添加到你的训练数据集可以提高模型的准确性。

## <a name="training-data-tips"></a>定型数据提示

务必要用于定型数据集进行了优化。 使用以下提示，以确保您获得最佳结果从[训练模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)操作：

* 如果可能，请使用基于文本的 PDF 文档，而不是基于映像的文档。 作为图像处理扫描的 Pdf。
* 如果您已拥有可，使用一个空窗体和两个已填写的窗体。
* 对于已填写的窗体，使用具有所有字段填充的示例。
* 使用窗体中的每个字段的值不同。
* 如果您窗体的图像的质量越低，请使用较大的数据集 （例如，10-15 图像）。

## <a name="general-input-requirements"></a>输入的一般要求

请确保你的训练数据集还遵循窗体识别器的所有内容的输入的要求。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>将定型数据上传

已放置在一起时的将用于定型的窗体文档集，您需要将其上载到 Azure blob 存储容器。 如果不知道如何创建用于容器的 Azure 存储帐户，遵循[Azure 存储快速入门： Azure 门户](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

### <a name="organize-your-data-in-subfolders-optional"></a>（可选） 的子文件夹中将数据组织

默认情况下[训练模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)API 将仅使用窗体位于存储容器的根目录的文档。 但是，如果指定的 API 调用中，你可以使用子文件夹中的数据训练。 通常情况下，正文[训练模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)调用具有以下形式，其中`<SAS URL>`是容器的共享访问签名 URL:

```json
{
  "source":"<SAS URL>"
}
```

如果将以下内容添加到请求正文中时，API 将定型的子文件夹中的文档。 `"prefix"`字段是可选的其路径以给定字符串开头的文件将训练数据集将限制。 因此值为`"Test"`，例如，将导致 API 来查看文件或单词"Test"开头的文件夹。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>后续步骤

现在，已了解如何构建一个定型数据集，请按照自定义窗体识别器模型定型并启动到窗体上使用的快速入门。

* [快速入门：训练模型并使用 cURL 提取窗体数据](./quickstarts/curl-train-extract.md)
* [快速入门：对模型进行定型和提取窗体数据使用 REST API 和 Python](./quickstarts/python-train-extract.md)

