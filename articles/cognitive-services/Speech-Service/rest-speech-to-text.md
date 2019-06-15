---
title: 语音转文本 API 参考 (REST)-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音到文本 REST API。 本文介绍授权选项、查询选项，以及如何构建请求和接收响应。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 4d5bceff004d1cc6ddac4046a2ddcd0b5f0b5e73
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072513"
---
# <a name="speech-to-text-rest-api"></a>语音转文本 REST API

作为一种替代方法[Speech SDK](speech-sdk.md)，语音服务允许将转换为语音到文本使用 REST API。 每个可访问的终结点都与某个区域相关联。 应用程序需要所用终结点的订阅密钥。

在使用语音到文本 REST API 之前, 了解：
* 使用 REST API 的请求只能包含录制的音频的 10 秒。
* 语音转文本 REST API 仅返回最终结果。 不提供部分结果。

如果必须为应用程序发送更长的音频，请考虑使用[语音 SDK](speech-sdk.md) 或[批处理脚本](batch-transcription.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>区域和终结点

使用 REST API 的语音转文本听录支持以下区域。 请务必选择与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>查询参数

可将以下参数包含在 REST 请求的查询字符串中。

| 参数 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| `language` | 标识所要识别的口语。 请参阅[支持的语言](language-support.md#speech-to-text)。 | 需要 |
| `format` | 指定结果格式。 接受的值为 `simple` 和 `detailed`。 简单结果包括 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 详细响应包括多个具有置信度值的结果，以及四种不同的表示形式。 默认设置是 `simple`。 | 可选 |
| `profanity` | 指定如何处理识别结果中的不雅内容。 接受的值为 `masked`（将亵渎内容替换为星号）、`removed`（删除结果中的所有亵渎内容）或 `raw`（包含结果中的亵渎内容）。 默认设置是 `masked`。 | 可选 |

## <a name="request-headers"></a>请求标头

下表列出了语音转文本请求的必需和可选标头。

|Header| 描述 | 必需/可选 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 你的语音服务的订阅密钥。 | 此标头或 `Authorization` 是必需的。 |
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 此标头或 `Ocp-Apim-Subscription-Key` 是必需的。 |
| `Content-type` | 描述所提供音频数据的格式和编解码器。 接受的值为 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必选 |
| `Transfer-Encoding` | 指定要发送分块的音频数据，而不是单个文件。 仅当要对音频数据进行分块时才使用此标头。 | 可选 |
| `Expect` | 如果使用分块传输，则发送 `Expect: 100-continue`。 语音服务确认初始请求并等待其他数据。| 如果发送分块的音频数据，则是必需的。 |
| `Accept` | 如果提供此标头，则值必须是 `application/json`。 语音服务提供 JSON 中的结果。 如果未指定默认值，则某些 Web 请求框架会提供不兼容的默认值，因此，最佳做法是始终包含 `Accept`。 | 可选，但建议提供。 |

## <a name="audio-formats"></a>音频格式

在 HTTP `POST` 请求的正文中发送音频。 它必须采用下表中的格式之一：

| 格式 | 编解码器 | Bitrate | 采样率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位 | 16 kHz，单声道 |
| OGG | OPUS | 16 位 | 16 kHz，单声道 |

>[!NOTE]
>通过 REST API 和语音服务中的 WebSocket 支持更高版本的格式。 [语音 SDK](speech-sdk.md) 目前仅支持使用 PCM 编解码器的 WAV 格式。

## <a name="sample-request"></a>示例请求

这是一个典型的 HTTP 请求。 以下示例包括主机名和必需的标头。 必须注意，服务同时预期提供音频数据，但此示例未包括这些数据。 如前所述，建议进行分块，但不是非要这样做。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| 100 | 继续 | 已接受初始请求。 继续发送剩余的数据。 （与分块传输配合使用。） |
| 200 | OK | 请求成功；响应正文是一个 JSON 对象。 |
| 400 | 错误的请求 | 语言代码未提供或不是支持的语言；音频文件无效。 |
| 401 | 未授权 | 指定区域中的订阅密钥或授权令牌无效，或终结点无效。 |
| 403 | 禁止 | 缺少订阅密钥或授权令牌。 |

## <a name="chunked-transfer"></a>分块传输

Chunked 传输 (`Transfer-Encoding: chunked`) 可以帮助减少识别的延迟，因为它允许语音服务以开始处理音频文件，而正在传输。 REST API 不提供部分结果或临时结果。 此选项专门用于改善响应能力。

此代码示例演示如何以块的形式发送音频。 只有第一个区块应该包含音频文件的标头。 `request` 是连接到相应 REST 终结点的 HTTPWebRequest 对象。 `audioFile` 是音频文件在磁盘上的路径。

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>响应参数

结果以 JSON 格式提供。 `simple` 格式包含以下顶级字段。

| 参数 | 描述  |
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

`detailed`格式包括相同的数据`simple`格式，以及与`NBest`，一组相同的识别结果的替代解释。 这些结果按从最有可能到最不可能。 第一个条目与主要识别结果相同。  使用 `detailed` 格式时，将以 `Display` 形式为 `NBest` 列表中的每条结果提供 `DisplayText`。

`NBest` 列表中的每个对象包括：

| 参数 | 描述 |
|-----------|-------------|
| `Confidence` | 条目的置信度评分，从 0.0（完全不可信）到 1.0（完全可信） |
| `Lexical` | 已识别文本的词法形式：识别的实际单词。 |
| `ITN` | 已识别文本的反向文本规范化（“规范”）形式，已应用电话号码、数字、缩写（“doctor smith”缩写为“dr smith”）和其他转换。 |
| `MaskedITN` | 可根据请求提供应用了亵渎内容屏蔽的 ITN 形式。 |
| `Display` | 已识别文本的显示形式，其中添加了标点符号和大小写形式。 此参数与将格式设置为 `simple` 时提供的 `DisplayText` 相同。 |

## <a name="sample-responses"></a>示例响应

这是 `simple` 识别的典型响应。

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

这是 `detailed` 识别的典型响应。

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
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
