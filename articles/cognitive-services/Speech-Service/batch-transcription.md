---
title: 什么是批量听录 - 语音服务
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
ms.openlocfilehash: 9804992aee318fdc34815bdbe4187144704cd667
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85099768"
---
# <a name="what-is-batch-transcription"></a>什么是批量听录？

批量听录是一组 REST API 操作，可用于听录存储中的大量音频。 你可以指向具有共享访问签名 (SAS) URI 的音频文件并异步接收听录结果。 使用新的 3.0 API，你可以选择转录一个或多个音频文件，或处理整个存储容器。

异步语音转文本听录只是其中的一项功能。 可以使用批量听录 REST API 调用以下方法：



|    批量听录操作                                             |    方法    |    REST API 调用                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    创建一个新的听录。                                              |    POST      |    speechtotext/3.0/转录            |
|    检索经过身份验证的订阅的听录列表。    |    GET       |    speechtotext/3.0/转录            |
|    获取脱机听录支持的区域设置列表。              |    GET       |    speechtotext/3.0/转录/区域设置    |
|    更新由 ID 标识的听录的可变详细信息。    |    PATCH     |    speechtotext/转录/{id}       |
|    删除指定的听录任务。                                 |    DELETE    |    speechtotext/转录/{id}       |
|    获取由给定 ID 标识的听录。                        |    GET       |    speechtotext/转录/{id}       |
|    获取由给定 ID 标识的脚本的结果文件。    |    GET       |    speechtotext/转录/{id}/files |




可以查看和测试详细的 API，该 API 可用作[Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)。

批量听录作业是按“尽力而为”的原则安排的。 目前，无法预估作业何时会变为正在运行状态。 在正常的系统负载下，几分钟内应该就可以发生该作业。 进入运行状态后，实际听录的处理速度比实时音频更快。

凭借易用的 API，无需部署自定义终结点，且无需遵守任何并发性要求。

## <a name="prerequisites"></a>先决条件

### <a name="subscription-key"></a>订阅密钥

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。

>[!NOTE]
> 若要使用批量听录，需要具备语音服务的标准订阅 (S0)。 免费订阅密钥 (F0) 无效。 有关详细信息，请参阅[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自定义模式

如果打算自定义模型，请按照[音频自定义](how-to-customize-acoustic-models.md)和[语言自定义](how-to-customize-language-model.md)中的步骤进行操作。 若要在 batch 脚本中使用创建的模型，需要其模型位置。 在检查模型的详细信息（属性）时，您可以检索模型位置 `self` 。 批处理脚本服务*不需要*已部署的自定义终结点。

## <a name="the-batch-transcription-api"></a>批量听录 API

### <a name="supported-formats"></a>支持的格式

批量听录 API 支持以下格式：

| 格式 | 编解码器 | 比特率 | 采样率                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 位  | 8 kHz 或 16 kHz，单声道或立体声 |
| MP3    | PCM   | 16 位  | 8 kHz 或 16 kHz，单声道或立体声 |
| OGG    | OPUS  | 16 位  | 8 kHz 或 16 kHz，单声道或立体声 |

对于立体声音频流，在听录期间会拆分左右声道。 对于每个声道，将创建一个 JSON 结果文件。 开发人员可利用为每个言语生成的时间戳创建有序的最终脚本。

### <a name="configuration"></a>配置

配置参数以 JSON 形式提供（一个或多个单独的文件）：

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

配置参数以 JSON （处理整个存储容器）的形式提供：

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

若要在 batch 转录中使用自定义训练模型，可以对其进行引用，如下所示：

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
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
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      指定如何处理识别结果中的不雅内容。 接受的值为 `None`（禁用不雅内容筛选）、`Masked`（将不雅内容替换为星号）、`Removed`（从结果中删除所有不雅内容）或 `Tags`（添加“不雅内容”标记）。 默认设置为 `Masked`。
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      指定如何处理识别结果中的标点。 接受的值为 `None`（禁用标点）、`Dictated`（暗示显式（口述）标点）、`Automatic`（让解码器处理标点），或 `DictatedAndAutomatic`（使用听写标点和自动标点）。 默认设置为 `DictatedAndAutomatic`。
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      指定是否应将字级时间戳添加到输出。 接受的值为 `true`（启用字级时间戳）和 `false`（默认值，禁用字级时间戳）。
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      指定应该对输入（预期为包含两个语音的单声道）执行分割聚类分析。 接受的值为 `true`（启用分割聚类）和 `false`（默认值，禁用分割聚类）。 还需要将 `wordLevelTimestampsEnabled` 设置为 true。
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      要处理的信道号的可选数组。 可在此处指定要处理的音频文件中的部分可用通道（例如 `0` 仅限）。 如果未指定，则通道 `0` 和 `1` 将转录为默认值。
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      在完成脚本后自动删除转录的可选持续时间。 在 `timeToLive` 批量处理转录中非常有用，可确保最终将其删除（例如 `PT12H` ）。 如果未指定或设置为 `PT0H` ，将不会自动删除脚本。
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure 中可写容器的可选 URL（包含[服务 SAS](../../storage/common/storage-sas-overview.md)）。 结果存储在此容器中。 如果未指定，Microsoft 会将结果存储在由 Microsoft 管理的存储容器中。 通过调用[Delete](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)脚本删除脚本时，结果数据也将被删除。
:::row-end:::

### <a name="storage"></a>存储

批量听录支持使用 [Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)来读取音频以及将听录内容写入存储。

## <a name="the-batch-transcription-result"></a>批量听录结果

对于每个输入音频，将创建一个脚本结果文件。 可以通过调用[get 转录 files](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles)获取结果文件的列表。 此方法返回此脚本的结果文件列表。 若要查找特定输入文件的脚本文件，请使用和筛选所有返回的文件 `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` 。

每个脚本结果文件的格式为：

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]    
    }
  ]
}
```

结果包含以下形式：

:::row:::
   :::column span="1":::
      **形式**
   :::column-end:::
   :::column span="2":::
      **内容**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      识别的实际单词。
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      已识别文本的反向文本规范化形式。 已应用缩写（“doctor smith”缩写为“dr smith”）、电话号码和其他转换。
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      应用了亵渎内容屏蔽的 ITN 形式。
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      已识别文本的显示形式。 包括添加的标点和大小写。
:::row-end:::

## <a name="speaker-separation-diarization"></a>讲述人分离（分割聚类）

分割聚类是将讲述人语音分隔成音频片段的过程。 Batch 管道支持分割聚类，并且能够识别单声道录制内容中的两个讲述人。 此功能不适用于立体声录音。

启用了 diarization 的脚本的输出中， `Speaker` 每个转录短语都包含一个条目。 如果未使用 diarization，则 `Speaker` JSON 输出中不存在该属性。 对于分割聚类，我们支持两段语音，因此讲述人标识为 `1` 或 `2`。

若要请求分割聚类，只需在 HTTP 请求中添加相关的参数，如下所示。

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

必须启用 Word 级时间戳，因为上述请求中的参数表示。

## <a name="best-practices"></a>最佳实践

听录服务可以处理大量的已提交听录内容。 可以通过 `GET` 中的[Get 转录](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)查询转录的状态。 检索结果后，调用从服务定期调用[Delete](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) 。 或者，将 `timeToLive` 属性设置为合理的值，以确保最终删除结果。

## <a name="sample-code"></a>代码示例

`samples/batch` 子目录内的 [GitHub 示例存储库](https://aka.ms/csspeech/samples)中提供了完整示例。

请使用你的订阅信息、服务区域、指向要转录的音频文件的 SAS URI 和模型位置（如果你想要使用自定义模型）来更新示例代码。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

示例代码设置客户端并提交听录请求。 然后，它会轮询状态信息并输出关于听录进度的详细信息。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

有关上述调用的完整详细信息，请参阅 [Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)。 有关此处所示的完整示例，请转到 `samples/batch` 子目录中的 [GitHub](https://aka.ms/csspeech/samples)。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是一个 .NET HTTP 客户端。 有一种 `PostTranscriptions` 方法可用于发送音频文件详细信息和 `GetTranscriptions` 用于接收状态的方法。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 服务使用基线模型来转录文件。 若要指定模型，可以对自定义模型的模型引用传递相同的方法。

> [!NOTE]
> 对于基线转录，无需声明基线模型的 ID。

## <a name="download-the-sample"></a>下载示例

可以在 [GitHub 示例存储库](https://aka.ms/csspeech/samples)的 `samples/batch` 目录中查找到该示例。

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
