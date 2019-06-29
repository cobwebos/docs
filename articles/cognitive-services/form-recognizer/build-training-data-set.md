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
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454814"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>生成用于自定义模型的定型数据集

使用窗体识别器自定义模型，以便于特定于行业的窗体可以训练该模型提供训练数据。 可以使用五个已填写的窗体或一个空窗体对模型进行定型 （包含单词"空"中的文件的名称） 以及两个已填写的窗体。 即使有足够已填写的窗体进行定型，将一个空窗体添加到你的训练数据集可以提高模型的准确性。

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

## <a name="next-steps"></a>后续步骤

现在，已了解如何构建一个定型数据集，请按照自定义窗体识别器模型定型并启动到窗体上使用的快速入门。

* [快速入门：训练模型并使用 cURL 提取窗体数据](./quickstarts/curl-train-extract.md)
* [快速入门：对模型进行定型和提取窗体数据使用 REST API 和 Python](./quickstarts/python-train-extract.md)

