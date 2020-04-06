---
title: 什么是批处理转录 - 语音服务
titleSuffix: Azure Cognitive Services
description: 如果要听录存储（如 Azure Blob）中的大量音频，则批量听录是理想的选择。 使用专用 REST API 可以通过共享访问签名 (SAS) URI 指向音频文件并异步接收听录。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: fb39f1ec83416ee8ab2a33b514971110db0c0b17
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668840"
---
# <a name="what-is-batch-transcription"></a>什么是批次转录？

批处理转录是一组 REST API 操作，使您能够在存储中转录大量音频。 您可以使用共享访问签名 （SAS） URI 指向音频文件，并异步接收转录结果。

异步语音转文本转录只是其中一个功能。 您可以使用批处理转录 REST API 调用以下方法：



|    批次转录操作                                             |    方法    |    REST API 调用                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    创建新的转录。                                              |    POST      |    api/语音文本/v2.0/转录            |
|    检索已身份验证订阅的转录列表。    |    GET       |    api/语音文本/v2.0/转录            |
|    获取脱机转录受支持区域设置的列表。              |    GET       |    api/语音文本/v2.0/转录/区域设置    |
|    更新其 ID 标识的转录的可变详细信息。    |    PATCH     |    api/语音文本/v2.0/转录/{id}       |
|    删除指定的转录任务。                                 |    DELETE    |    api/语音文本/v2.0/转录/{id}       |
|    获取给定 ID 标识的转录。                        |    GET       |    api/语音文本/v2.0/转录/{id}       |




您可以在标题`Custom Speech transcriptions`下查看和测试详细的 API，该 API 可作为[Swagger 文档](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)提供。

批量听录作业是按“尽力而为”的原则安排的。 当前，无法估计作业何时更改为运行状态。 在正常的系统负载下，几分钟内应该就可以发生该作业。 进入运行状态后，实际听录的处理速度比实时音频更快。

凭借易用的 API，无需部署自定义终结点，且无需遵守任何并发性要求。

## <a name="prerequisites"></a>先决条件

### <a name="subscription-key"></a>订阅密钥

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。

>[!NOTE]
> 若要使用批量听录，需要具备语音服务的标准订阅 (S0)。 免费订阅密钥 （F0） 不起作用。 有关详细信息，请参阅[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自定义模式

如果你打算自定义声学或语言模型，请遵循[自定义声学模型](how-to-customize-acoustic-models.md)和[设计自定义语言模型](how-to-customize-language-model.md)中的步骤。 若要在批量听录中使用所创建的模型，需要提供其模型 ID。 可以在检查模型的详细信息时检索模型 ID。 批量听录服务不需要部署的自定义终结点。

## <a name="the-batch-transcription-api"></a>Batch 听录 API

### <a name="supported-formats"></a>支持的格式

Batch 听录 API 支持以下格式：

| 格式 | 编解码器 | Bitrate | 采样率                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 位  | 8 kHz 或 16 kHz，单声道或立体声 |
| MP3    | PCM   | 16 位  | 8 kHz 或 16 kHz，单声道或立体声 |
| OGG    | OPUS  | 16 位  | 8 kHz 或 16 kHz，单声道或立体声 |

对于立体声音频流，在听录期间会拆分左右声道。 对于每个声道，将创建一个 JSON 结果文件。 开发人员可利用为每个言语生成的时间戳创建有序的最终脚本。

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
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>配置属性

使用以下可选属性来配置听录：

:::row:::
   :::column span="1":::
      **参数**
   :::column-end:::
   :::column span="2":::
      **说明**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      指定如何处理识别结果中的不雅内容。 接受的值是`None`禁用亵渎筛选，`Masked`用星号取代亵渎，`Removed`从结果中删除所有亵渎，或`Tags`添加"亵渎"标记。 默认设置为 `Masked`。
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      指定如何处理识别结果中的标点。 接受的值是`None`禁用标点符号，`Dictated`表示显式（口头）标点符号，`Automatic`让解码器处理标点符号，或使用`DictatedAndAutomatic`听写和自动标点符号。 默认设置为 `DictatedAndAutomatic`。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      指定是否应将字级时间戳添加到输出。 接受的值是`true`启用单词级时间戳和`false`（默认值）来禁用它。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      指定是否应将情绪分析应用于陈述。 接受的值是`true`启用和`false`（默认值）来禁用它。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      指定应在输入上执行二分化分析，该输入预期是包含两个语音的单声道。 接受的值启用`true`二分化和`false`（默认值）来禁用它。 还需要将 `AddWordLevelTimestamps` 设置为 true。
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure 中可写容器的可选 URL（包含[服务 SAS](../../storage/common/storage-sas-overview.md)）。 结果存储在此容器中。
:::row-end:::

### <a name="storage"></a>存储

批量听录支持使用 [Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)来读取音频以及将听录内容写入存储。

## <a name="the-batch-transcription-result"></a>批量听录结果

对于单声道输入音频，将创建一个听录结果文件。 对于立体声输入音频，将创建两个听录结果文件。 每个文件采用以下结构：

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
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
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

| Form        | 内容                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | 识别的实际单词。                                                                                                                             |
| `ITN`       | 已识别文本的反向文本规范化形式。 已应用缩写（“doctor smith”缩写为“dr smith”）、电话号码和其他转换。 |
| `MaskedITN` | 应用了亵渎内容屏蔽的 ITN 形式。                                                                                                             |
| `Display`   | 已识别文本的显示形式。 包括添加的标点符号和大小写。                                                             |

## <a name="speaker-separation-diarization"></a>讲述人分离（分割聚类）

分割聚类是将讲述人语音分隔成音频片段的过程。 Batch 管道支持分割聚类，并且能够识别单声道录制内容中的两个讲述人。 此功能不适用于立体声录音。

所有听录输出包含一个 `SpeakerId`。 如果未使用二分法，则它在 JSON 输出中显示`"SpeakerId": null`。 对于二分化，我们支持两个声音，因此扬声器被标识为`"1"``"2"`或 。

若要请求分割聚类，只需在 HTTP 请求中添加相关的参数，如下所示。

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

如上述请求中的参数所示，还必须“启用”单词级时间戳。

## <a name="sentiment-analysis"></a>情绪分析

情绪分析功能评估音频中表达的情绪。 情绪由 0 到 1 的值表示，分别表示 `Negative`、`Neutral` 和 `Positive` 情绪。 例如，可以在呼叫中心方案中使用情绪分析：

- 获取有关客户满意度的见解
- 获取有关座席（受理来电的团队）绩效的见解
- 找出某次通话改变消极结局的确切时间点
- 将消极通话转变为积极通话时的良好交互情况
- 了解客户对某个产品或服务喜欢和不喜欢的方面

情绪根据词法形式按音频段评分。 该音频段内的整个文本用于计算情绪。 不会计算整个听录的聚合情绪。 目前情绪分析仅适用于英语。

下面是 JSON 输出示例：

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

## <a name="best-practices"></a>最佳做法

听录服务可以处理大量的已提交听录内容。 可以通过[听录方法](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)中的 `GET` 查询听录的状态。 通过指定 `take` 参数来保持以合理的大小（数百）返回信息。 检索结果后，定期从服务中[删除听录](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription)。 这保证了从转录管理调用的快速答复。

## <a name="sample-code"></a>示例代码

`samples/batch` 子目录内的 [GitHub 示例存储库](https://aka.ms/csspeech/samples)中提供了完整示例。

如要使用自定义声学或语言模型，必须使用订阅信息、服务区域、指向要转录的音频文件的 SAS URI 和模型 ID 来自定义示例代码。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

示例代码设置客户端并提交转录请求。 然后，它会轮询状态信息并打印有关转录进度的详细信息。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

有关上述呼叫的完整详细信息，请参阅我们的[斯瓦格文档](https://westus.cris.ai/swagger/ui/index)。 有关此处所示的完整示例，请转到 `samples/batch` 子目录中的 [GitHub](https://aka.ms/csspeech/samples)。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是一个 .NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 该服务使用基线模型来听录一个或多个文件。 若要指定模型，可为声学和语言模型传递与模型 ID 相同的方法。

> [!NOTE]
> 对于基线听录，无需声明基线模型的 ID。 如果只指定语言模型 ID（而不指定声学模型 ID），则自动选择匹配的声学模型。 如果只指定声学模型 ID，则自动选择匹配的语言模型。

## <a name="download-the-sample"></a>下载示例

可以在 [GitHub 示例存储库](https://aka.ms/csspeech/samples)的 `samples/batch` 目录中查找到该示例。

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
