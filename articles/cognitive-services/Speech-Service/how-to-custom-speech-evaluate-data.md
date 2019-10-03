---
title: 评估自定义语音语音服务的准确性
titleSuffix: Azure Cognitive Services
description: 在本文档中，你将学习如何而言度量语音到文本模型或自定义模型的质量。 需要音频和人为标记的脚本数据来测试准确性，并且应提供30分钟到5小时的代表音频。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 77dc50df7b46aeb6ddfddf92710d576f85492471
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801540"
---
# <a name="evaluate-custom-speech-accuracy"></a>评估自定义语音准确性

在本文档中，你将学习如何而言度量 Microsoft 的语音到文本模型或自定义模型的质量。 需要音频和人为标记的脚本数据来测试准确性，并且应提供30分钟到5小时的代表音频。

## <a name="what-is-word-error-rate-wer"></a>什么是 Word 错误率（WER）？

度量模型准确性的行业标准是*Word 错误率*（WER）。 WER 计算在识别期间标识的错误单词数，然后除以用户标记的脚本中提供的字数。 最后，此数字乘以 100% 来计算 WER。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

错误识别的单词分为三个类别：

* 插入（I）：在假设脚本中错误添加的字词
* 删除（D）：在假设脚本中未检测到的字词
* 替换：在 reference 和假设间替换的单词

以下是一个示例：

![错误标识单词的示例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解决错误并改善 WER

你可以从计算机识别结果使用 WER 来评估你在应用、工具或产品中使用的模型的质量。 5%-10% 的 WER 被视为良好的质量，并且已准备好使用。 可接受 20% 的 WER，但你可能需要考虑其他培训。 30% 或更多信号的 WER 质量较差，需要自定义和培训。

如何分发错误非常重要。 遇到许多删除错误时，通常是由于音频信号强度弱导致的。 若要解决此问题，需要收集离源更近的音频数据。 插入错误意味着音频记录在干扰环境中并且可能出现 crosstalk，从而导致识别问题。 当以人为标记的转录或相关文本提供的域特定术语的示例不足时，通常会遇到替换错误。

通过分析单个文件，您可以确定存在哪种类型的错误，以及哪些错误对于特定文件是唯一的。 了解文件级别的问题可帮助你定位改进。

## <a name="create-a-test"></a>创建测试

如果要测试 Microsoft 语音到文本基线模型的质量或已定型的自定义模型的质量，可以比较两个模型，以便评估准确性。 比较包含 WER 和识别结果。 通常，自定义模型与 Microsoft 的基线模型比较。

并行计算模型：

1. 登录到[自定义语音门户](https://speech.microsoft.com/customspeech)。
2. 导航到 "**语音到文本" > 自定义语音 > 测试**。
3. 单击 "**添加测试**"。
4. 选择 "**评估准确性**"。 为测试指定名称、说明，并选择音频 + 人标记的脚本数据集。
5. 最多选择两个想要测试的模型。
6. 单击“创建”。

成功创建测试后，可以并排比较结果。

## <a name="side-by-side-comparison"></a>并行比较

测试完成后，将状态更改为 "*成功*" 后，你会发现测试中包含的两个模型都有一个 WER 号。 单击测试名称以查看 "测试详细信息" 页。 此详细信息页列出了数据集中的所有最谈话，指明了两种模型的识别结果以及提交的数据集的脚本。 若要帮助检查并排比较，可以切换各种错误类型，包括插入、删除和替换。 通过倾听音频，并比较每个列中的识别结果，其中显示了两个语音到文本模型的人为标记的脚本和结果，您可以决定哪个模型满足您的需要，以及其他培训和改进必填。

## <a name="next-steps"></a>后续步骤

* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
