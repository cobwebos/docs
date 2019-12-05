---
title: 准备自定义语音语音服务的测试数据
titleSuffix: Azure Cognitive Services
description: 无论您是要测试 Microsoft 语音识别的准确性还是训练您自己的模型，都需要数据（以音频和/或文本的形式）。 在此页上，我们介绍了数据类型、数据的使用方式以及如何对其进行管理。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: b2d02ed5a9fb2cb10e4cf18fe7d878da5b032fe0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816403"
---
# <a name="prepare-data-for-custom-speech"></a>准备自定义语音的数据

无论你是在测试中查看 Microsoft 语音识别的准确性，还是需要培训自己的模型，你都需要采用音频和文本形式的数据。 在此页上，我们介绍了数据类型、数据的使用方式以及如何对其进行管理。

## <a name="data-types"></a>数据类型

此表列出了可接受的数据类型，何时使用每个数据类型，以及建议的数量。 不是每个数据类型都需要创建模型。 数据要求将因你创建的是测试还是定型模型而异。

| 数据类型 | 测试使用 | 建议数量 | 用于定型 | 建议数量 |
|-----------|-----------------|----------|-------------------|----------|
| [音频](#audio-data-for-testing) | 是<br>用于视觉检测 | 5 + 音频文件 | No | N/A |
| [音频和人为标记的脚本](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用于评估准确性 | 0.5-5 小时的音频 | 是 | 1-1000 小时的音频 |
| [相关文本](#related-text-data-for-training) | No | N/A | 是 | 1-200 MB 相关文本 |

文件应按类型分组，并上传为 zip 文件。 每个数据集只能包含一种数据类型。

## <a name="upload-data"></a>上载数据

准备好上传数据时，导航到[自定义语音门户](https://speech.microsoft.com/customspeech)，然后单击 "**上传数据**" 以启动向导并创建第一个数据集。 系统会要求你为数据集选择语音数据类型，然后才能上传数据。

![从语音门户选择音频](./media/custom-speech/custom-speech-select-audio.png)

你上载的每个数据集都必须满足你选择的数据类型的要求。 在上载数据之前，请务必正确设置数据的格式。 这可确保自定义语音服务能够准确处理数据。 以下各节中列出了这些要求。

上载数据集后，您可以使用几个选项：

* 你可以导航到 "**测试**" 选项卡，直观地检查 "仅音频" 或 "音频 + 人标记的脚本数据"。
* 您可以导航到 "**训练**" 选项卡，使用 "音频 + 人脚本" 数据或相关文本数据来训练自定义模型。

## <a name="audio-data-for-testing"></a>用于测试的音频数据

音频数据最适合用于测试 Microsoft 基线语音到文本模型或自定义模型的准确性。 请记住，音频数据用于检查语音与特定模型性能的准确性。 如果希望量化模型的准确性，请使用[音频和人标记](#audio--human-labeled-transcript-data-for-testingtraining)的脚本数据。

使用此表可以确保正确设置音频文件的格式，以便与自定义语音一起使用：

| properties | Value |
|----------|-------|
| 文件格式 | RIFF (WAV) |
| 采样速率 | 8000 hz 或 16000 Hz |
| 频道 | 1（单声道） |
| 每个音频的最大长度 | 2 小时 |
| 示例格式 | PCM，16 位 |
| 存档格式 | .zip |
| 最大存档大小 | 2GB |

如果您的音频不满足这些属性，或者您想要检查它是否有，我们建议您下载[sox](http://sox.sourceforge.net)来检查或转换音频。 下面的示例演示如何通过命令行完成每个活动：

| 活动 | 描述 | Sox 命令 |
|----------|-------------|-------------|
| 检查音频格式 | 使用此命令检查音频文件格式。 | `sox --i <filename>` |
| 转换音频格式 | 使用此命令可将音频文件转换为单通道，16位，16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>音频 + 用人标记的脚本数据进行测试/培训

若要在处理音频文件时测量 Microsoft 语音到文本准确性的准确性，必须提供人机标记的转录（单词字词）进行比较。 尽管人为标记的脚本通常非常耗时，但还是需要评估准确性并为用例定型模型。 请记住，识别的改进仅适用于所提供的数据。 出于此原因，仅上传高质量的脚本很重要。  

| properties | Value |
|----------|-------|
| 文件格式 | RIFF (WAV) |
| 采样速率 | 8000 hz 或 16000 Hz |
| 频道 | 1（单声道） |
| 每个音频的最大长度 | 60秒 |
| 示例格式 | PCM，16 位 |
| 存档格式 | .zip |
| 最大 zip 大小 | 2GB |

若要解决 word 删除或替换等问题，需要大量数据来改善识别。 通常，建议提供大约10到1000小时的音频的单词转录。 应在单个纯文本文件中包含所有 WAV 文件的听录。 听录文件的每一行应包含一个音频文件的名称，后接相应的听录。 文件名和听录应以制表符 (\t) 分隔。

  例如：
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> 听录应编码为 UTF-8 字节顺序标记 (BOM)。

听录内容应经过文本规范化，以便可由系统处理。 但是，在将数据上传到 Speech Studio_之前_，用户必须完成一些重要的 normalizations。 若要在准备转录时使用合适的语言，请参阅[如何创建带标签的](how-to-custom-speech-human-labeled-transcriptions.md)脚本

收集音频文件和相应的转录后，应在上传到[自定义语音门户](https://speech.microsoft.com/customspeech)之前将它们打包为一个 .zip 文件。 这是一个包含三个音频文件和一个人标记的脚本文件的示例数据集：

![从语音门户选择音频](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>用于定型的相关文本数据

如果你具有唯一的产品名称或功能，并且想要确保它们已正确识别，则必须包含用于定型的相关文本数据。 可提供两种类型的相关文本数据来改善识别：

| 数据类型 | 此数据如何改进识别 |
|-----------|------------------------------------|
| 最谈话和/或句子 | 当识别句子上下文中的产品名称或特定于行业的词汇时，这些内容可以提高准确性。 |
| 发音 | 这可以改进不常见字词、缩写词或其他不带发音的单词的发音。 |

最谈话可以作为单个或多个文本文件提供。 文本数据越接近于所讲述的内容，准确性越大，性能越好。 发音应以单个文本文件的形式提供。 可以将所有内容打包为一个 zip 文件并将其上传到[自定义语音门户](https://speech.microsoft.com/customspeech)。

### <a name="guidelines-to-create-an-utterances-file"></a>创建最谈话文件的准则

若要使用相关文本创建自定义模型，需要提供示例最谈话列表。 这些最谈话不需要完整的句子或语法上的正确语法，但必须准确反映您在生产中预期的口头输入。 如果你希望某些术语具有更大的权重，则可以将多个句子添加到相关数据文件，其中包含这些特定字词。

使用此表可确保最谈话的相关数据文件的格式正确：

| properties | Value |
|----------|-------|
| 文本编码 | UTF-8 BOM |
| 每行的话语数 | 第 |
| 文件大小上限 | 200 MB |

此外，您还需要考虑以下限制：

* 避免重复字符四次以上。 例如： "aaaa" 或 "uuuu"。
* 不要在 U + 00A1 以上使用特殊字符或 UTF-8 字符。
* 将拒绝 Uri。

### <a name="guidelines-to-create-a-pronunciation-file"></a>创建发音文件的准则

如果你的用户将遇到或不需要使用标准发音的罕见术语，则可以提供自定义发音文件来改善识别。

> [!IMPORTANT]
> 不建议使用此功能来更改常用词语的发音。

其中包括一个口述的查询文本示例，以及每个的自定义发音：

| 已识别/显示的窗体 | 口头形式 |
|--------------|--------------------------|
| 3CPO | three c p o |  
| CNTK | c n t k |
| IEEE | i triple e |

口述的窗体是拼写出的拼音顺序。它可以由字母、字、音节或这三三项的组合组成。

自定义发音提供英语（zh-cn）和德语（de）。 此表按语言显示支持的字符：

| 语言 | Locale | 字符 |
|----------|--------|------------|
| 英语 | en-US | a、b、c、d、e、f、g、h，i，j，k，l，m，n，o，p，q，r，s，t，u，v，w，x，y，z |
| 德语 | de-DE | ä，ö，ü，a，b，c，d，e，f，g，h，i，j，k，l，m，n，o，p，q，r，s，t，u，v，w，x，y，z |

使用此表可确保发音的相关数据文件的格式正确。 发音文件较小，不应超过几 Kb。

| properties | Value |
|----------|-------|
| 文本编码 | UTF-8 BOM （英语还支持 ANSI） |
| 每行的发音数 | 第 |
| 文件大小上限 | 1 MB （1 KB 用于免费层） |

## <a name="next-steps"></a>后续步骤

* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
