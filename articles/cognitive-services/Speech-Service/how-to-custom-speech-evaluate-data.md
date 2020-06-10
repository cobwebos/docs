---
title: 评估自定义语音识别的准确度 - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文档介绍如何以定量方式度量我们的语音转文本模型或你的自定义模型的质量。 需要使用音频 + 人为标记的听录数据来测试准确度，并应提供 30 分钟到 5 小时的代表性音频。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 27229d36dbe9592b6ad6fc2740f7714f99fd6857
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629064"
---
# <a name="evaluate-custom-speech-accuracy"></a>评估自定义语音识别准确度

本文档介绍如何以定量方式度量 Microsoft 的语音转文本模型或你的自定义模型的质量。 需要使用音频 + 人为标记的听录数据来测试准确度，并应提供 30 分钟到 5 小时的代表性音频。

## <a name="what-is-word-error-rate-wer"></a>什么是误字率 (WER)？

用于度量模型准确度的行业标准是误字率  (WER)。 计算 WER 时，先对识别过程中标识的错误单词计数，然后将其除以人为标记的听录中提供的单词的总数， 最后将该数字乘以 100%。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

错误标识的单词分为三个类别：

* 插入 (I)：在假设脚本中错误添加的单词
* 删除 (D)：在假设脚本中未检测到的单词
* 替换 (S)：在引用和假设之间替换的单词

下面是一个示例：

![错误标识的单词的示例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解决错误并降低 WER

可以使用机器识别结果中的 WER 来评估与应用、工具或产品配合使用的模型的质量。 WER 为 5%-10% 表明质量好，可以使用。 WER 为 20% 可以接受，但可能需要考虑进行更多的训练。 WER 为 30% 或以上表明质量差，需要自定义和训练。

错误的分布情况很重要。 如果遇到许多删除错误，通常是因为音频信号强度弱。 若要解决此问题，需要在收集音频数据时更靠近源。 插入错误意味着音频是在嘈杂环境中记录的，并且可能存在串音，导致识别问题。 如果以人为标记的听录或相关文本形式提供特定于领域的术语样本不足，则通常会遇到替换错误。

可以通过分析单个文件来确定存在的错误的类型，以及哪些错误是特定文件独有的。 在文件级别了解问题将有助于你确定改进目标。

## <a name="create-a-test"></a>创建测试

若要测试 Microsoft 的语音转文本基线模型或你训练的自定义模型的质量，可以将两个模型并排比较一下，评估准确度。 此比较包括 WER 和识别结果。 通常情况下，自定义模型会与 Microsoft 的基线模型比较。

若要并排评估模型，请执行以下操作：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。
2. 导航到 "**语音到文本" > 自定义语音 > [项目名称] > 测试**。
3. 单击“添加测试”。 
4. 选择“评估准确度”。  为测试提供名称和说明，然后选择你的音频和人为标记的听录数据集。
5. 选择最多两个要测试的模型。
6. 单击**创建**。

成功创建测试后，可以并排比较结果。

## <a name="side-by-side-comparison"></a>并排比较

测试完成（状态更改为“成功”即表明完成）后，就可以找到测试中包括的两个模型的 WER 值。  单击测试名称可查看测试详细信息页。 该详细信息页会列出数据集中的所有言语，指示两个模型的识别结果以及提供的数据集中的听录。 可以通过切换各种错误类型（包括插入、删除和替换）来查看并排比较的结果。 通过听音频并比较每个列（显示人为标记的听录和两个语音转文本模型的结果）中的识别结果，你可以确定哪个模型符合自己的需求，以及需要在哪些方面进行更多的训练和改进。

## <a name="next-steps"></a>后续步骤

* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
