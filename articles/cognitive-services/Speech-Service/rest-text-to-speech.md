---
title: 文本到语音转换 API 参考 (REST)-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用文本到语音转换的 REST API。 本文介绍授权选项、查询选项，以及如何构建请求和接收响应。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d687665a7f9b18d42dcd45953c15d2fbc6d71d2f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335821"
---
# <a name="text-to-speech-rest-api"></a>文本转语音 REST API

语音服务允许将转换为文本到语音转换使用 REST API。 每个可访问的终结点都与某个区域相关联。 应用程序需要所用终结点的订阅密钥。 文本转语音 REST API 支持神经和标准文本转语音，每种语音支持区域设置标识的特定语言和方言。

* 有关语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。
* 有关区域可用性的信息，请参阅[区域](regions.md#text-to-speech)。

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的费用各不相同。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

在使用此 API 之前了解：

* 文本转语音 REST API 需要授权标头。 这意味着，需要完成令牌交换才能访问该服务。 有关详细信息，请参阅[身份验证](#authentication)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>区域和终结点

使用 REST API 的语音转文本听录支持以下区域。 请务必选择与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="request-headers"></a>请求标头

下表列出了语音转文本请求的必需和可选标头。

| 标头 | 描述 | 必需/可选 |
|--------|-------------|---------------------|
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 需要 |
| `Content-Type` | 指定所提供的文本的内容类型。 接受的值：`application/ssml+xml`。 | 需要 |
| `X-Microsoft-OutputFormat` | 指定音频输出格式。 有关接受值的完整列表，请参阅[音频输出](#audio-outputs)。 | 需要 |
| `User-Agent` | 应用程序名称。 提供的值必须是少于 255 个字符。 | 需要 |

## <a name="audio-outputs"></a>音频输出

这是在每个请求中作为 `X-Microsoft-OutputFormat` 标头发送的受支持音频格式的列表。 每种格式合并了比特率和编码类型。 语音服务支持 24 KHz，16 KHz 和 8 KHz 音频输出。

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> 如果所选语音和输出格式具有不同的比特率，则根据需要对音频重新采样。 但是，24khz 语音不支持 `audio-16khz-16kbps-mono-siren` 和 `riff-16khz-16kbps-mono-siren` 输出格式。

## <a name="request-body"></a>请求正文

每个 `POST` 请求的正文作为[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 发送。 SSML 允许选择文本到语音转换服务返回的合成语音的语音和语言。 有关受支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

> [!NOTE]
> 如果使用自定义语音，请求正文可以作为纯文本（ASCII 或 UTF-8）发送。

## <a name="sample-request"></a>示例请求

此 HTTP 请求使用 SSML 指定语音和语言。 正文不能超过 1,000 个字符。

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

有关特定于语言的示例根据快速入门，请参阅：

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | OK | 请求成功；响应正文是一个音频文件。 |
| 400 | 错误的请求 | 必需参数缺失、为空或为 null。 或者，传递给必需参数或可选参数的值无效。 常见问题是标头太长。 |
| 401 | 未授权 | 请求未经授权。 确保订阅密钥或令牌有效并在正确的区域中。 |
| 413 | 请求实体太大 | SSML 输入超过了 1024 个字符。 |
| 429 | 请求过多 | 已经超过了订阅允许的配额或请求速率。 |
| 502 | 错误的网关 | 网络或服务器端问题。 也可能表示标头无效。 |

如果 HTTP 状态为 `200 OK`，则响应正文包含采用所请求格式的音频文件。 可以一边传输一边播放此文件，或者将其保存到缓冲区或文件中。

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
