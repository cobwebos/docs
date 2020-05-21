---
title: 训练自定义语音识别模型 - 语音服务
titleSuffix: Azure Cognitive Services
description: 训练语音转文本模型可以提高 Microsoft 的基线模型或某个自定义模型的识别准确度。 模型使用人为标记的听录和相关的文本进行训练。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 09867d6739998902ea0fd11281d1b975866c1242
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722620"
---
# <a name="train-a-model-for-custom-speech"></a>训练自定义语音识别模型

训练语音转文本模型可以提高 Microsoft 的基线模型的识别准确度。 模型使用人为标记的听录和相关的文本进行训练。 这些数据集和以前上传的音频数据用来优化和训练语音转文本模型，使之能够识别单词、短语、缩写词、名称和其他特定于产品的术语。 提供的领域内数据集（与用户所说的内容以及你预期要识别的内容相关的数据）越多，模型越准确，因此会改进识别。 请注意，将不相关的数据馈送到训练中可能会降低或损害模型的准确度。

## <a name="use-training-to-resolve-accuracy-issues"></a>通过训练解决准确度问题

如果模型出现识别问题，则使用人为标记的听录和相关的数据进行更多训练有助于提高准确度。 使用此表可确定应使用哪个数据集来解决问题：

| 使用案例 | 数据类型 |
| -------- | --------- |
| 提高特定于行业的词汇和语法（例如医疗术语或 IT 行话）的识别准确度。 | 相关的文本（句子/言语） |
| 定义发音不标准的字词或术语（例如产品名或首字母缩写）的语音和显示形式。 | 相关的文本（发音） |
| 提高说话风格、口音或特定背景杂音的识别准确度。 | 音频和人为标记的听录内容 |

> [!IMPORTANT]
> 如果尚未上传数据集，请参阅[准备和测试数据](how-to-custom-speech-test-data.md)。 本文档说明如何上传数据，并指导你创建高质量数据集。

## <a name="train-and-evaluate-a-model"></a>训练和评估模型

训练模型的第一步是上传训练数据。 请参阅[准备和测试数据](how-to-custom-speech-test-data.md)以获取分步说明，了解如何准备人为标记的听录和相关的文本（言语和发音）。 上传训练数据以后，请按以下说明开始训练模型：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。
2. 导航到“语音转文本”&gt;“自定义语音识别”&gt;“训练”。 
3. 单击“训练模型”。 
4. 接下来，为训练提供**名称**和**说明**。
5. 从“方案和基线模型”  下拉菜单中，选择最适合你的领域的方案。 如果不确定要选择哪个方案，请选择“通用”。  该基线模型是训练的起点。 最新的模型通常是最佳选择。
6. 在“选择训练数据”页中，选择一个或多个要用于训练的音频和人为标记的听录数据集。 
7. 完成训练后，可以选择对新训练的模型执行准确度测试。 此步骤是可选的。
8. 选择“创建”，生成自定义模型。 

“训练”表将显示对应于此新建模型的新条目。 该表还会显示以下状态：“正在处理”、“成功”、“失败”。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>评估已训练模型的准确度

可以按照以下文档的说明检查数据并评估模型准确度：

- [检查数据](how-to-custom-speech-inspect-data.md)
- [评估数据](how-to-custom-speech-evaluate-data.md)

如果选择测试准确性，请务必选择与模型中使用的数据集不同的声音数据集，以获得模型性能的真实认识。

## <a name="next-steps"></a>后续步骤

- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

- [准备和测试数据](how-to-custom-speech-test-data.md)
- [检查数据](how-to-custom-speech-inspect-data.md)
- [评估数据](how-to-custom-speech-evaluate-data.md)
- [训练模型](how-to-custom-speech-train-model.md)
