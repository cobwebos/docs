---
title: 如何使用批操作-语音服务
titleSuffix: Azure Cognitive Services
description: 如果要听录存储（如 Azure Blob）中的大量音频，则批量听录是理想的选择。 使用专用 REST API 可以通过共享访问签名 (SAS) URI 指向音频文件并异步接收听录。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 6b23ae21366699162b900ae420afae640aa20613
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921477"
---
# <a name="how-to-use-batch-transcription"></a>如何使用批处理脚本

批处理脚本非常适合用于在存储空间中转录大量音频。 通过使用专用的 REST API，可以使用共享访问签名（SAS） URI 指向音频文件，并异步接收脚本结果。

该 API 提供异步语音到文本脚本和其他功能。 您可以使用 REST API 公开方法：

- 创建批处理请求
- 查询状态
- 下载脚本结果
- 从服务中删除脚本信息

详细的 API 以[Swagger 文档](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)形式提供，位于 `Custom Speech transcriptions`的标题下。

最大程度地计划批处理脚本作业。 当前，在作业更改为 "正在运行" 状态时，不会进行估计。 在正常系统负载下，应该在几分钟内发生。 进入 "正在运行" 状态后，处理实际脚本的速度要快于音频实时。

在易于使用的 API 旁，你无需部署自定义终结点，并且你无需执行任何并发性要求。

## <a name="prerequisites"></a>必备组件

### <a name="subscription-key"></a>订阅密钥

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。

>[!NOTE]
> 需要语音服务的标准订阅（S0）才能使用批处理脚本。 免费订阅密钥 (F0) 不可用。 有关详细信息，请参阅[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自定义模式

如果打算自定义声音或语言模型，请按照[自定义声音模型](how-to-customize-acoustic-models.md)和[设计自定义语言模型](how-to-customize-language-model.md)中的步骤操作。 若要在 batch 脚本中使用创建的模型，则需要其模型 Id。 您可以在检查模型的详细信息时检索模型 ID。 批处理脚本服务不需要已部署的自定义终结点。

## <a name="the-batch-transcription-api"></a>Batch 听录 API

### <a name="supported-formats"></a>支持的格式

Batch 听录 API 支持以下格式：

| 格式 | 编解码器 | Bitrate | 采样率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位 | 8 kHz 或 16 kHz、单声道或立体声 |
| MP3 | PCM | 16 位 | 8 kHz 或 16 kHz、单声道或立体声 |
| OGG | OPUS | 16 位 | 8 kHz 或 16 kHz、单声道或立体声 |

对于立体声音频流，会在脚本中拆分左右声道。 对于每个通道，将创建一个 JSON 结果文件。 每个查询文本生成的时间戳使开发人员可以创建排序的最终脚本。

### <a name="configuration"></a>配置

配置参数以 JSON 形式提供：

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<SAS to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>配置属性

使用以下可选属性来配置脚本：

| 参数 | Description |
|-----------|------------|
|`ProfanityFilterMode`|指定如何处理识别结果中的猥亵
||**`Masked`** 默认值。 将猥亵替换为星号<br>`None`-禁用猥亵语言筛选<br>`Removed`-从结果中删除所有猥亵<br>`Tags`-添加猥亵语言标记
|`PunctuationMode`|指定在识别结果中处理标点符号
||`Automatic`-服务插入标点<br>`Dictated` 口述的（口述）标点符号<br>**`DictatedAndAutomatic`** 默认值。 听写和自动标点<br>`None`-禁用标点
|`AddWordLevelTimestamps`|指定是否应将 word 级别的时间戳添加到输出
||`True`-启用 word 级别的时间戳<br>**`False`** 默认值。 禁用 word 级别的时间戳
|`AddSentiment`|指定是否将情绪分析添加到查询文本
||`True`-启用每个查询文本的情绪<br>**`False`** 默认值。 禁用情绪
|`AddDiarization`|指定是否执行 diarization 分析。如果 `true`，则输入应为单声道通道音频，最多包含两个声音。 需要将 `AddWordLevelTimestamps` 设置为 `true`
||`True`-启用 diarization<br>**`False`** 默认值。 禁用 diarization
|`TranscriptionResultsContainerUrl`|可选 SAS 令牌到 Azure 中的可写容器。 结果将存储在此容器中

### <a name="storage"></a>存储空间

批处理脚本支持[Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)，用于读取音频并将转录写入存储。

## <a name="the-batch-transcription-result"></a>批脚本结果

对于 mono 输入音频，将创建一个脚本结果文件。 对于立体声输入音频，将创建两个脚本结果文件。 每个都具有以下结构：

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                     'for each individual segment'
        {
          "RecognitionStatus": Success | Failure
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                     'null if no diarization
                                                            or stereo input file, the
                                                            speakerId as a string if
                                                            diarization requested for
                                                            mono audio file'
          "Offset": number                                'time in milliseconds'
          "Duration": number                              'time in milliseconds'
          "OffsetInSeconds" : number                      'Real number. Two decimal places'
          "DurationInSeconds" : number                    'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                        'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                          'this is omitted if sentiment is
                                                            not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                         'time in milliseconds'
                  "Duration": number                       'time in milliseconds'
                  "OffsetInSeconds": number                'Real number. Two decimal places'
                  "DurationInSeconds": number              'Real number. Two decimal places'
                  "Confidence": number                     'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

结果包含以下形式：

|Form|内容|
|-|-|
|`Lexical`|识别的实际字词。
|`ITN`|已识别文本的反转文本规范化形式。 将应用缩写（"王 smith" 到 "dr smith"）、电话号码和其他转换。
|`MaskedITN`|应用了猥亵屏蔽的故障窗体。
|`Display`|可识别文本的显示形式。 这包括添加的标点和大小写。

## <a name="speaker-separation-diarization"></a>演讲者分离（Diarization）

Diarization 是将扬声器分离成一片音频的过程。 批处理管道支持 diarization，并且能够识别 mono 通道录制上的两个扬声器。 此功能不适用于立体声录音。

所有脚本输出都包含一个 `SpeakerId`。 如果未使用 diarization，则它将在 JSON 输出中显示 `"SpeakerId": null`。 对于 diarization，我们支持两个声音，因此扬声器会被标识为 `"1"` 或 `"2"`。

若要请求 diarization，只需将相关参数添加到 HTTP 请求中，如下所示。

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

由于上述请求中的参数表明，Word 级时间戳还必须 "打开"。

## <a name="sentiment-analysis"></a>观点分析

情绪功能估算音频中表示的情绪。 情绪由介于0和1之间的值表示 `Negative`、`Neutral`和 `Positive` 情绪。 例如，情绪分析可用于呼叫中心方案：

- 深入了解客户满意度
- 深入了解代理的性能（执行调用的团队）
- 查找调用负方向时的确切时间点
- 如果将负调调为正方向，会发生什么情况
- 确定用户喜欢的内容及其对产品或服务不喜欢的内容

情绪根据词法形式每个音频段进行评分。 该音频段内的整个文本用于计算情绪。 没有为整个脚本计算聚合情绪。

JSON 输出示例如下所示：

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>最佳实践

脚本服务可处理大量提交的转录。 可以通过对[转录方法](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)的 `GET` 查询转录的状态。 通过指定 `take` 参数（几百个），将信息恢复到合理的大小。 检索结果后，请定期从服务中[删除转录](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription)。 这将保证快速响应脚本管理呼叫。

## <a name="sample-code"></a>代码示例

`samples/batch` 子目录内的[GitHub 示例存储库](https://aka.ms/csspeech/samples)中提供了完整的示例。

如要使用自定义声学或语言模型，必须使用订阅信息、服务区域、指向要转录的音频文件的 SAS URI 和模型 ID 来自定义示例代码。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

示例代码将设置客户端并提交脚本请求。 然后它将轮询状态信息并打印关于转录进度的详细信息。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

有关上述调用的完整详细信息，请参阅 [Swagger 文档](https://westus.cris.ai/swagger/ui/index)。 有关此处所示的完整示例，请转到 `samples/batch` 子目录中的 [GitHub](https://aka.ms/csspeech/samples)。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是一个 .NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 该服务使用基线模型来听录一个或多个文件。 若要指定模型，可为声学和语言模型传递与模型 ID 相同的方法。

> [!NOTE]
> 对于基线听录，无需声明基线模型的 ID。 如果只指定语言模型 ID（而不指定声学模型 ID），则自动选择匹配的声学模型。 如果只指定声学模型 ID，则自动选择匹配的语言模型。

## <a name="download-the-sample"></a>下载示例

可以在 [GitHub 示例存储库](https://aka.ms/csspeech/samples)的 `samples/batch` 目录中查找到该示例。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
