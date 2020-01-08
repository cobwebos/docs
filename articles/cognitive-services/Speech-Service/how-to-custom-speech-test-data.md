---
title: 准备自定义语音语音服务的测试数据
titleSuffix: Azure Cognitive Services
description: 在测试 Microsoft speech 识别的准确性或定型您的自定义模型时，您将需要音频和文本数据。 在此页上，我们介绍了数据类型、使用方法和管理数据。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 6100ac6a6b01a7d0eac74b0e83539bf4e671cb89
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660403"
---
# <a name="prepare-data-for-custom-speech"></a>准备自定义语音的数据

在测试 Microsoft speech 识别的准确性或定型您的自定义模型时，您将需要音频和文本数据。 在此页上，我们介绍了数据类型、使用方法和管理数据。

## <a name="data-types"></a>数据类型

此表列出了可接受的数据类型，何时使用每个数据类型，以及建议的数量。 不是每个数据类型都需要创建模型。 数据要求将因你创建的是测试还是定型模型而异。

| 数据类型 | 用于测试 | 建议数量 | 用于定型 | 建议数量 |
|-----------|-----------------|----------|-------------------|----------|
| [音频](#audio-data-for-testing) | 是<br>用于视觉检测 | 5 + 音频文件 | 否 | N/A |
| [音频和人为标记的脚本](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用于评估准确性 | 0.5-5 小时的音频 | 是 | 1-1000 小时的音频 |
| [相关文本](#related-text-data-for-training) | 否 | N/A | 是 | 1-200 MB 相关文本 |

文件应按类型分组，并上传为 .zip 文件。 每个数据集只能包含一种数据类型。

> [!TIP]
> 若要快速开始使用，请考虑使用示例数据。 请参阅此 GitHub 存储库，了解<a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">示例自定义语音数据<span class="docon docon-navigate-external x-hidden-focus"></span> </a>

## <a name="upload-data"></a>上载数据

若要上传数据，请导航到<a href="https://speech.microsoft.com/customspeech" target="_blank">自定义语音<span class="docon docon-navigate-external x-hidden-focus"></span>门户</a>。 从门户中，单击 "**上传数据**" 以启动向导并创建第一个数据集。 系统会要求你为数据集选择语音数据类型，然后才能上传数据。

![从语音门户选择音频](./media/custom-speech/custom-speech-select-audio.png)

你上载的每个数据集都必须满足你选择的数据类型的要求。 数据必须在上传之前正确格式化。 格式正确的数据确保自定义语音服务能够准确地处理数据。 以下各节中列出了这些要求。

上载数据集后，您可以使用几个选项：

* 你可以导航到 "**测试**" 选项卡，直观地检查 "仅音频" 或 "音频 + 人标记的脚本数据"。
* 您可以导航到 "**训练**" 选项卡，使用 "音频 + 人脚本" 数据或相关文本数据来训练自定义模型。

## <a name="audio-data-for-testing"></a>用于测试的音频数据

音频数据最适合用于测试 Microsoft 基线语音到文本模型或自定义模型的准确性。 请记住，音频数据用于检查语音与特定模型性能的准确性。 如果希望量化模型的准确性，请使用[音频和人标记](#audio--human-labeled-transcript-data-for-testingtraining)的脚本数据。

使用此表可以确保正确设置音频文件的格式，以便与自定义语音一起使用：

| 属性 | 值 |
|----------|-------|
| 文件格式 | RIFF (WAV) |
| 采样速率 | 8000 hz 或 16000 Hz |
| 频道 | 1（单音） |
| 每个音频的最大长度 | 2 小时 |
| 示例格式 | PCM，16 位 |
| 存档格式 | .zip |
| 最大存档大小 | 2GB |

> [!TIP]
> 上传定型和测试数据时，.zip 文件的大小不能超过 2 GB。 如果需要更多的培训数据，请将其划分为多个 .zip 文件并分别上传。 稍后，您可以选择从*多个*数据集进行训练。 但是，只能从*单个*数据集进行测试。

使用<a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a>验证音频属性，或将现有音频转换为适当的格式。 以下示例说明了如何通过 SoX 命令行完成每个活动：

| 活动 | Description | SoX 命令 |
|----------|-------------|-------------|
| 检查音频格式 | 使用此命令检查<br>音频文件格式。 | `sox --i <filename>` |
| 转换音频格式 | 使用此命令转换<br>单个通道的音频文件，16位，16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>音频 + 用人标记的脚本数据进行测试/培训

若要在处理音频文件时测量 Microsoft 语音到文本准确性的准确性，必须提供人机标记的转录（单词字词）进行比较。 尽管人为标记的脚本通常非常耗时，但还是需要评估准确性并为用例定型模型。 请记住，识别的改进仅适用于所提供的数据。 出于此原因，仅上传高质量的脚本很重要。

| 属性 | 值 |
|----------|-------|
| 文件格式 | RIFF (WAV) |
| 采样速率 | 8000 hz 或 16000 Hz |
| 频道 | 1（单音） |
| 每个音频的最大长度 | 2小时（测试）/60 秒（培训） |
| 示例格式 | PCM，16 位 |
| 存档格式 | .zip |
| 最大 zip 大小 | 2GB |

> [!NOTE]
> 上传定型和测试数据时，.zip 文件的大小不能超过 2 GB。 Uou 只能从*单个*数据集进行测试，请确保将其保持在适当的文件大小内。

若要解决 word 删除或替换等问题，需要大量数据来改善识别。 通常，建议提供大约10到1000小时的音频的单词转录。 应在单个纯文本文件中包含所有 WAV 文件的听录。 听录文件的每一行应包含一个音频文件的名称，后接相应的听录。 文件名和听录应以制表符 (\t) 分隔。

  例如：
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> 听录应编码为 UTF-8 字节顺序标记 (BOM)。

听录内容应经过文本规范化，以便可由系统处理。 但是，在将数据上传到 Speech Studio 之前，必须完成一些重要的 normalizations。 若要在准备转录时使用合适的语言，请参阅[如何创建带标签的](how-to-custom-speech-human-labeled-transcriptions.md)脚本

收集音频文件和相应的转录后，请在上载到<a href="https://speech.microsoft.com/customspeech" target="_blank">自定义语音门户<span class="docon docon-navigate-external x-hidden-focus"> </span></a>之前将它们打包为一个 .zip 文件。 下面是一个包含三个音频文件和一个人标记的脚本文件的示例数据集：

> [!div class="mx-imgBorder"]
> ![从语音门户选择音频](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>用于定型的相关文本数据

产品名称或功能是唯一的，应包含用于定型的相关文本数据。 相关文本有助于确保正确识别。 可提供两种类型的相关文本数据来改善识别：

| 数据类型 | 此数据如何改进识别 |
|-----------|------------------------------------|
| 句子（最谈话） | 在句子上下文中识别产品名称或行业特定词汇时，提高准确性。 |
| 发音 | 通过未定义的发音提高常见术语、缩写词或其他字词的发音。 |

句子可以作为单个文本文件或多个文本文件提供。 若要提高准确性，请使用比预期的口头最谈话更近的文本数据。 发音应以单个文本文件的形式提供。 可以将所有内容打包为一个 zip 文件并将其上传到<a href="https://speech.microsoft.com/customspeech" target="_blank">自定义语音门户<span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。

### <a name="guidelines-to-create-a-sentences-file"></a>创建句子文件的准则

若要使用句子创建自定义模型，需要提供示例最谈话列表。 最谈话_无_需完整或语法正确，但必须准确反映在生产中预期的所需的朗读输入。 如果希望某些术语具有更大的权重，请添加包含这些特定术语的多个句子。

一般原则是，当定型文本尽可能接近生产环境中的实际文本时，模型改写最有效。 要增强的特定于域的术语和短语应包含在定型文本中。 如果可能，请尝试在单独的行中控制一个句子或关键字。 对于对你很重要的关键字和短语（例如产品名称），可以将其复制几次。 但请记住，不要复制太多内容，这可能会影响总体识别速率。

使用此表可确保最谈话的相关数据文件的格式正确：

| 属性 | 值 |
|----------|-------|
| 文本编码 | UTF-8 BOM |
| 每行的话语数 | 第 |
| 文件大小上限 | 200 MB |

此外，您还需要考虑以下限制：

* 避免重复字符四次以上。 例如： "aaaa" 或 "uuuu"。
* 请勿在 `U+00A1`以上使用特殊字符或 UTF-8 字符。
* 将拒绝 Uri。

### <a name="guidelines-to-create-a-pronunciation-file"></a>创建发音文件的准则

如果你的用户将遇到或不需要使用标准发音的罕见术语，则可以提供自定义发音文件来改善识别。

> [!IMPORTANT]
> 不建议使用自定义发音文件来更改常用词语的发音。

其中包括一个口述的查询文本示例，以及每个的自定义发音：

| 已识别/显示的窗体 | 口头形式 |
|--------------|--------------------------|
| 3CPO | three c p o |
| CNTK | c n t k |
| IEEE | i triple e |

口述的窗体是拼写出的拼音顺序。它可以由字母、字、音节或这三三项的组合组成。

自定义发音提供英语（`en-US`）和德语（`de-DE`）。 此表按语言显示支持的字符：

| 语言 | 区域设置 | 字符 |
|----------|--------|------------|
| 英语 | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| 德语 | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

使用下表来确保发音的相关数据文件格式正确。 发音文件较小，只应使用几 kb 的大小。

| 属性 | 值 |
|----------|-------|
| 文本编码 | UTF-8 BOM （英语还支持 ANSI） |
| 每行的发音数 | 第 |
| 文件大小上限 | 1 MB （1 KB 用于免费层） |

## <a name="next-steps"></a>后续步骤

* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
