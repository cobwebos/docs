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
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: e98ac335f475e4a6db6b17d5954512f5749e6aec
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881158"
---
# <a name="why-use-batch-transcription"></a>为何使用 Batch 听录？

如果要听录存储（如 Azure Blob）中的大量音频，则批量听录是理想的选择。 使用专用 REST API 可以通过共享访问签名 (SAS) URI 指向音频文件并异步接收听录。

## <a name="prerequisites"></a>先决条件

### <a name="subscription-key"></a>订阅密钥

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。 如果计划从基线模型获取听录，则需要创建一个密钥。

>[!NOTE]
> 若要使用批量听录，需要具备语音服务的标准订阅 (S0)。 免费订阅密钥 (F0) 不可用。 有关详细信息，请参阅[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自定义模式

如果计划自定义声学或语言模型，请遵循[自定义声学模型](how-to-customize-acoustic-models.md)和[自定义语言模型](how-to-customize-language-model.md)中的步骤。 若要在批量听录中使用所创模型，则需要其模型 ID。 此 ID 不是在“终结点详细信息”视图中查找到的终结点 ID，而是选择模型“详细信息”时可检索到的模型 ID。

## <a name="the-batch-transcription-api"></a>Batch 听录 API

Batch 听录 API 提供异步语音转文本听录和其他功能。 它是一个 REST API，可以公开用于以下目的的方法：

1. 创建批处理请求
1. 查询状态
1. 下载听录

> [!NOTE]
> Batch 听录 API 非常适合经常累积数千小时音频的呼叫中心。 它使转录大量录音变得简单。

### <a name="supported-formats"></a>支持的格式

Batch 听录 API 支持以下格式：

| 格式 | 编解码器 | Bitrate | 采样率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位 | 8 或 16 kHz、单声道、立体声 |
| MP3 | PCM | 16 位 | 8 或 16 kHz、单声道、立体声 |
| OGG | OPUS | 16 位 | 8 或 16 kHz、单声道、立体声 |

对于立体声音频流，Batch 听录 API 将在听录期间分离左右声道。 根据单个通道创建两个带有结果的 JSON 文件。 开发人员可利用每个话语的时间戳创建有序的最终脚本。 此示例请求包括用于不雅内容筛选、标点符号和字级时间戳的属性。

### <a name="configuration"></a>配置

配置参数以 JSON 形式提供：

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Batch 听录 API 使用 REST 服务来请求听录内容、听录状态和相关结果。 可在任何语言中使用该 API。 下一部分将介绍该 API 的用法。

### <a name="configuration-properties"></a>配置属性

使用以下可选属性来配置脚本:

| 参数 | 描述 |
|-----------|-------------|
| `ProfanityFilterMode` | 指定如何处理识别结果中的不雅内容。 接受的值为 `None`（禁用不雅内容筛选）、`masked`（将不雅内容替换为星号）、`removed`（从结果中删除所有不雅内容）或 `tags`（添加“不雅内容”标记）。 默认设置是 `masked`。 |
| `PunctuationMode` | 指定如何处理识别结果中的标点。 接受的值为 `None`（禁用标点）、`dictated`（表示使用显式标点）、`automatic`（允许解码器处理标点）或 `dictatedandautomatic`（表示使用专用标点符号或自动使用标点）。 |
 | `AddWordLevelTimestamps` | 指定是否应将字级时间戳添加到输出。 接受的值为 `true`，其支持字级时间戳和 `false`（默认值）禁用它。 |
 | `AddSentiment` | 指定应将情绪添加到查询文本中。 接受`true`的值允许每个查询文本的情绪`false`和 (默认值) 禁用。 |
 | `AddDiarization` | 指定应在输入上执行 diarization alalysis, 该输入应是包含两个声音的 mono 通道。 接受`true`的值使 diarization 和`false` (默认值) 可以禁用它。 还需要`AddWordLevelTimestamps`将设置为 true。|

### <a name="storage"></a>存储

批处理脚本支持[Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview), 用于读取音频并将转录写入存储。

## <a name="webhooks"></a>Webhooks

轮询脚本状态可能不是最高性能, 也可能是最佳的用户体验。 若要轮询状态, 可以注册回调, 这会在长时间运行的脚本任务完成时通知客户端。

有关更多详细信息, 请参阅[webhook](webhooks.md)。

## <a name="speaker-separation-diarization"></a>演讲者分离 (Diarization)

Diarization 是将扬声器分离成一片音频的过程。 批处理管道支持 Diarization, 并且能够识别 mono 通道录制上的两个扬声器。

若要请求为 diarization 处理音频脚本请求, 只需在 HTTP 请求中添加相关参数, 如下所示。

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

由于上述请求中的参数指示, Word 级别时间戳还必须 "打开"。

对应的音频将包含由号码标识的扬声器 (当前仅支持两个语音, 因此扬声器将标识为 "演讲者 1" 和 "音箱 2"), 后接脚本输出。

另请注意, Diarization 不能用于立体声录音。 此外, 所有 JSON 输出都将包含发言人标记。 如果未使用 diarization, 则会显示 "演讲者:JSON 输出中的 Null "。

> [!NOTE]
> Diarization 在所有区域和所有区域设置中都可用!

## <a name="sentiment"></a>情绪

情绪是批处理脚本中的一项新功能, 是呼叫中心域中的一项重要功能。 客户可以使用其`AddSentiment`请求的参数

1.  获取有关客户满意度的见解
2.  深入了解代理的性能 (执行调用的团队)
3.  找出调用负方向的确切时间点
4.  确定在对正调用负
5.  确定用户喜欢的内容及其对产品或服务不喜欢的内容

情绪按每个音频段评分, 其中音频段定义为查询文本 (偏移) 开始与字节流结束的检测无声之间的时间间隔。 该段内的整个文本用于计算情绪。 我们不会为整个调用或每个通道的整个语音计算任何聚合情绪值。 这些聚合将留给域所有者进一步应用。

情绪应用于词法窗体。

JSON 输出示例如下所示:

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
此功能使用的是情绪模型, 该模型当前为 Beta 版本。

## <a name="sample-code"></a>示例代码

`samples/batch`子目录中的[GitHub 示例存储库](https://aka.ms/csspeech/samples)中提供了完整的示例。

如要使用自定义声学或语言模型，必须使用订阅信息、服务区域、指向要转录的音频文件的 SAS URI 和模型 ID 来自定义示例代码。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

示例代码将设置客户端并提交转录请求。 然后它将轮询状态信息并打印关于转录进度的详细信息。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

有关上述调用的完整详细信息，请参阅 [Swagger 文档](https://westus.cris.ai/swagger/ui/index)。 有关此处所示的完整示例，请转到 `samples/batch` 子目录中的 [GitHub](https://aka.ms/csspeech/samples)。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是一个 .NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 该服务使用基线模型来听录一个或多个文件。 若要指定模型，可为声学和语言模型传递与模型 ID 相同的方法。

> [!NOTE]
> 对于基线听录，无需声明基线模型的 ID。 如果只指定语言模型 ID（而不指定声学模型 ID），则自动选择匹配的声学模型。 如果只指定声学模型 ID，则自动选择匹配的语言模型。

## <a name="download-the-sample"></a>下载示例

可以在 [GitHub 示例存储库](https://aka.ms/csspeech/samples)的 `samples/batch` 目录中查找到该示例。

> [!NOTE]
> 批量听录作业是在最大努力的基础上进行安排的，没有对作业何时会变为运行状态作时间估计。 进入运行状态后，实际转录处理速度比实时音频快。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
