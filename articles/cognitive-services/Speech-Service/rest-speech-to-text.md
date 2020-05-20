---
title: 语音转文本 API 参考 (REST) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音转文本 REST API。 本文介绍授权选项、查询选项，以及如何构建请求和接收响应。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: yinhew
ms.openlocfilehash: 555ae9e48f538c1100bab8b35ce61742baa88451
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659837"
---
# <a name="speech-to-text-rest-api"></a>语音转文本 REST API

作为[语音 SDK](speech-sdk.md) 的一种替代方法，语音服务允许使用 REST API 转换语音转文本。 每个可访问的终结点都与某个区域相关联。 应用程序需要所用终结点的订阅密钥。 REST API 非常有限，只应在[语音 SDK](speech-sdk.md) 不能使用的情况下使用。

使用语音转文本 REST API 之前，请先了解：

* 使用 REST API 并直接传输音频的请求最多只能包含 60 秒的音频。
* 语音转文本 REST API 仅返回最终结果。 不提供部分结果。

如果应用程序需要发送更长的音频，请考虑使用[语音 SDK](speech-sdk.md) 或基于文件的 REST API，如[批量转录](batch-transcription.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>区域和终结点

REST API 的终结点具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必须将语言参数追加到 URL 以避免收到 4xx HTTP 错误。 例如，使用“美国西部”终结点设置为美国英语的语言为：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

## <a name="query-parameters"></a>查询参数

可将以下参数包含在 REST 请求的查询字符串中。

| 参数 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `language` | 标识所要识别的口语。 请参阅[支持的语言](language-support.md#speech-to-text)。 | 必需 |
| `format` | 指定结果格式。 接受的值为 `simple` 和 `detailed`。 简单结果包括 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 详细的响应包括四种不同的显示文本表示形式。 默认设置为 `simple`。 | 可选 |
| `profanity` | 指定如何处理识别结果中的不雅内容。 接受的值为 `masked`（将亵渎内容替换为星号）、`removed`（删除结果中的所有亵渎内容）或 `raw`（包含结果中的亵渎内容）。 默认设置为 `masked`。 | 可选 |
| `cid` | 使用[自定义语音门户](how-to-custom-speech.md)创建自定义模型时，可以通过在“部署”  页上找到的其终结点 ID  使用自定义模型。 使用终结点 ID  作为 `cid` 查询字符串形式参数的实际参数。 | 可选 |

## <a name="request-headers"></a>请求标头

下表列出了语音转文本请求的必需和可选标头。

|标头| 说明 | 必需/可选 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 语音服务订阅密钥。 | 此标头或 `Authorization` 是必需的。 |
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 此标头或 `Ocp-Apim-Subscription-Key` 是必需的。 |
| `Pronunciation-Assessment` | 指定用于在识别结果中显示发音评分的参数，这些参数可评估语音输入的发音质量，并显示准确性、熟练、完整性等。此参数是 base64 编码的 json，其中包含多个详细参数。 有关如何生成此标头，请参阅[发音评估参数](#pronunciation-assessment-parameters)。 | 可选 |
| `Content-type` | 描述所提供音频数据的格式和编解码器。 接受的值为 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必需 |
| `Transfer-Encoding` | 指定要发送分块的音频数据，而不是单个文件。 仅当要对音频数据进行分块时才使用此标头。 | 可选 |
| `Expect` | 如果使用分块传输，则发送 `Expect: 100-continue`。 语音服务将确认初始请求并等待附加的数据。| 如果发送分块的音频数据，则是必需的。 |
| `Accept` | 如果提供此标头，则值必须是 `application/json`。 语音服务以 JSON 格式提供结果。 某些请求框架提供不兼容的默认值。 最好始终包含 `Accept`。 | 可选，但建议提供。 |

## <a name="audio-formats"></a>音频格式

在 HTTP `POST` 请求的正文中发送音频。 它必须采用下表中的格式之一：

| 格式 | 编解码器 | 比特率 | 采样率  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz，单声道 |
| OGG    | OPUS  | 256 kpbs | 16 kHz，单声道 |

>[!NOTE]
>通过语音服务中的 REST API 和 WebSocket 支持上述格式。 [语音 SDK](speech-sdk.md) 当前支持使用 PCM 编解码器的 WAV 格式以及[其他格式](how-to-use-codec-compressed-audio-input-streams.md)。

## <a name="pronunciation-assessment-parameters"></a>发音评估参数

此表列出了发音评估的必需参数和可选参数。

| 参数 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| ReferenceText | 将对发音进行计算的文本。 | 必需 |
| GradingSystem | 用于分数校准的点系统。 接受的值为 `FivePoint` 和 `HundredMark`。 默认设置为 `FivePoint`。 | 可选 |
| 粒度 | 计算粒度。 接受的值为 `Phoneme` ，其中显示了全文本、单词和音素级别上的分数， `Word` 其中显示了整个文本和 word 级别的分数， `FullText` 只显示了完整文本级别的分数。 默认设置为 `Phoneme`。 | 可选 |
| 维度 | 定义输出条件。 接受的值为 `Basic` ，只显示精确度评分， `Comprehensive` 显示更多维度上的分数（例如，熟练分数和完整文本级别的完整性分数，word 级别上的错误类型）。 检查[响应参数](#response-parameters)以查看不同分数维度和 word 错误类型的定义。 默认设置为 `Basic`。 | 可选 |
| EnableMiscue | 启用 miscue 计算。 启用此功能后，会将发音为的单词与引用文本进行比较，并根据比较结果标记为省略/插入。 接受的值为 `False` 和 `True`。 默认设置为 `False`。 | 可选 |
| ScenarioId | 指示自定义点系统的 GUID。 | 可选 |

下面是一个示例 JSON，其中包含发音评估参数：

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

下面的示例代码演示如何将发音评估参数生成到 `Pronunciation-Assessment` 标头中：

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

>[!NOTE]
>发音评估功能当前仅适用于 `westus` 和 `eastasia` 区域。 此功能目前仅适用于 `en-US` 语言。

## <a name="sample-request"></a>示例请求

以下示例包括主机名和必需的标头。 必须注意，服务同时预期提供音频数据，但此示例未包括这些数据。 如前所述，建议进行分块，但不是非要这样做。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

若要启用发音评估，可以添加以下标头。 有关如何生成此标头，请参阅[发音评估参数](#pronunciation-assessment-parameters)。

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

## <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | 说明 | 可能的原因 |
|------------------|-------------|-----------------|
| `100` | 继续 | 已接受初始请求。 继续发送剩余的数据。 （与分块传输配合使用） |
| `200` | OK | 请求成功；响应正文是一个 JSON 对象。 |
| `400` | 错误的请求 | 语言代码未提供、不是支持的语言、音频文件无效等。 |
| `401` | 未授权 | 指定区域中的订阅密钥或授权令牌无效，或终结点无效。 |
| `403` | 禁止 | 缺少订阅密钥或授权令牌。 |

## <a name="chunked-transfer"></a>分块传输

分块传输 (`Transfer-Encoding: chunked`) 有助于降低识别延迟。 它允许语音服务在传输音频文件时开始处理该文件。 REST API 不提供部分结果或临时结果。

此代码示例演示如何以块的形式发送音频。 只有第一个区块应该包含音频文件的标头。 `request` 是连接到相应 REST 终结点的 `HttpWebRequest` 对象。 `audioFile` 是音频文件在磁盘上的路径。

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>响应参数

结果以 JSON 格式提供。 `simple` 格式包含以下顶级字段。

| 参数 | 说明  |
|-----------|--------------|
|`RecognitionStatus`|状态，例如 `Success` 表示成功识别。 请参阅下表。|
|`DisplayText`|经过大小写转换、添加标点、执行反向文本规范化（将口头文本转换为短形式，例如，200 表示“two hundred”，或“Dr.Smith”表示“doctor smith”）和屏蔽亵渎内容之后的识别文本。 仅在成功时提供。|
|`Offset`|在音频流中开始识别语音的时间（以 100 纳秒为单位）。|
|`Duration`|在音频流中识别语音的持续时间（以 100 纳秒为单位）。|

`RecognitionStatus` 字段可包含以下值：

| 状态 | 描述 |
|--------|-------------|
| `Success` | 识别成功并且存在 `DisplayText` 字段。 |
| `NoMatch` | 在音频流中检测到语音，但没有匹配目标语言的字词。 通常表示识别语言不同于讲话用户所用的语言。 |
| `InitialSilenceTimeout` | 音频流的开始仅包含静音，并且服务在等待语音时超时。 |
| `BabbleTimeout` | 音频流的开始仅包含噪音，并且服务在等待语音时超时。 |
| `Error` | 识别服务遇到内部错误，无法继续。 如果可能，请重试。 |

> [!NOTE]
> 如果音频仅包含亵渎内容，并且 `profanity` 查询参数设置为 `remove`，则服务不会返回语音结果。

`detailed`格式包括更多形式的可识别结果。
使用 `detailed` 格式时，将以 `Display` 形式为 `NBest` 列表中的每条结果提供 `DisplayText`。

列表中的对象 `NBest` 可以包括：

| 参数 | 说明 |
|-----------|-------------|
| `Confidence` | 条目的置信度评分，从 0.0（完全不可信）到 1.0（完全可信） |
| `Lexical` | 已识别文本的词法形式：识别的实际单词。 |
| `ITN` | 已识别文本的反向文本规范化（“规范”）形式，已应用电话号码、数字、缩写（“doctor smith”缩写为“dr smith”）和其他转换。 |
| `MaskedITN` | 可根据请求提供应用了亵渎内容屏蔽的 ITN 形式。 |
| `Display` | 已识别文本的显示形式，其中添加了标点符号和大小写形式。 此参数与将格式设置为 `simple` 时提供的 `DisplayText` 相同。 |
| `AccuracyScore` | 指示给定语音的读音准确性的分数。 |
| `FluencyScore` | 指示给定语音的熟练的分数。 |
| `CompletenessScore` | 该分数指示给定语音的完整性，方法是计算要向整个输入的单词的比率。 |
| `PronScore` | 指示给定语音的发音质量的总体分数。 这是通过和权重计算得出的 `AccuracyScore` `FluencyScore` `CompletenessScore` 。 |
| `ErrorType` | 此值指示与相对应的字是省略、插入还是不正确 `ReferenceText` 。 可能的值为 `None` （表示此词上无错误） `Omission` 、 `Insertion` 和 `Mispronunciation` 。 |

## <a name="sample-responses"></a>示例响应

`simple` 识别的典型响应：

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` 识别的典型响应：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

使用发音评估进行识别的典型响应：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
