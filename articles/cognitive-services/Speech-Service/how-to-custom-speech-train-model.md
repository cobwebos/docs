---
title: 训练自定义语音语音服务的模型
titleSuffix: Azure Cognitive Services
description: 若要改善 Microsoft 的基线模型或计划创建的自定义模型的识别准确性, 必须训练语音到文本。 使用带有人标记的转录和相关文本对模型进行训练。 这些数据集以及以前上传的音频数据用于优化和训练语音到文本模型, 以识别单词、短语、缩写词、名称和其他特定于产品的术语。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b5893b4f07444b07bf142971a5df4776e549d307
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562822"
---
# <a name="train-a-model-for-custom-speech"></a>为自定义语音定型模型

若要改善 Microsoft 的基线模型或计划创建的自定义模型的识别准确性, 必须训练语音到文本。 使用带有人标记的转录和相关文本对模型进行训练。 这些数据集以及以前上传的音频数据用于优化和训练语音到文本模型, 以识别单词、短语、缩写词、名称和其他特定于产品的术语。 您提供的域中的数据集越多 (与用户将显示的内容以及您希望识别的内容相关的数据), 您的模型就越精确, 这会使识别改进。 请记住, 通过将不相关的数据放入您的培训中, 可以降低或损害模型的准确性。

## <a name="use-training-to-resolve-accuracy-issues"></a>使用培训解决准确性问题

如果你遇到与模型有关的识别问题, 则使用带标签的脚本和相关数据进行其他训练有助于提高准确性。 使用此表来确定用于解决问题的数据集:

| 使用案例 | 数据类型 |
|----------|-----------|
| 提高特定于行业的词汇和语法的识别准确性, 如医疗术语或 IT 术语 | 相关文本 (句子/最谈话) |
| 定义包含非标准发音 (如产品名称或首字母缩写词) 的单词或字词的拼音和显示形式。 | 相关文本 (发音) |
| 提高对讲话风格、强调或特定背景噪音的识别准确性 | 音频和人为标记的脚本 |
> [!IMPORTANT]
> 如果尚未上传数据集, 请参阅[准备和测试数据](how-to-custom-speech-test-data.md)。 本文档提供有关如何上传数据的说明, 以及用于创建高质量数据集的准则。

## <a name="train-and-evaluate-a-model"></a>训练和评估模型

训练模型的第一步是上传定型数据。 使用 "[准备和测试数据](how-to-custom-speech-test-data.md)" 以获取有关准备人为标签的转录和相关文本 (最谈话和发音) 的分步说明。 上传定型数据后, 请按照以下说明开始定型模型:

1. 导航到 "**语音到文本" > 自定义语音 > "培训**"。
2. 单击 "**训练模型**"。
3. 接下来, 为训练指定**名称**和**说明**。
4. 从 "**方案和基线模型**" 下拉菜单中, 选择最适合你的域的方案。 如果你不确定选择哪种方案, 请选择 "**常规**"。 基线模型是定型的起点。 如果没有首选项, 可以使用最新的。
5. 从 "**选择定型数据**" 页中, 选择一个或多个要用于定型的音频和人标记的脚本数据集。
6. 完成培训后, 您可以选择对新训练的模型执行准确性测试。 此步骤是可选的。
7. 选择 "**创建**" 以生成自定义模型。

定型表显示与这个新创建的模型相对应的新条目。 该表还显示状态:处理、成功、失败。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>评估定型模型的准确性

您可以使用以下文档检查数据并评估模型准确性:

* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)


如果选择测试准确性, 请务必选择与模型中使用的数据集不同的声音数据集, 以获得模型性能的真实认识。

## <a name="next-steps"></a>后续步骤

* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
