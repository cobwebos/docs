---
title: 通过语音服务使用“文本转语音”功能
description: 了解如何使用语音服务中的“文本转语音”功能。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 21157af9ad6361234ffa9b927da64baab505f624
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281508"
---
# <a name="use-text-to-speech-in-speech-service"></a>使用语音服务中的“文本转语音”功能

通过简单的 HTTP 请求即可获取语音服务的“文本转语音”功能。 通过 POST 方法将要口述的文本发布到相应的终结点，然后服务即会返回包含合成语音的音频文件 (`.wav`)。 之后，应用程序可根据需要使用此音频。

文本转语音的 POST 请求的正文可为纯文本（ASCII 或 UTF8），也可为 [SSML](speech-synthesis-markup.md) 文档。 使用默认语音说出纯文本请求。 多数情况下建议使用 SSML 正文。 HTTP 请求必须包含[授权](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication)令牌。 

区域性的“文本转语音”终结点显示如下。 请使用适合你的订阅的终结点。

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>指定语音

要指定语音，请使用 `<voice>` [SSML](speech-synthesis-markup.md) 标记。 例如：

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

有关可用语音及其名称的列表，请参阅[“文本转语音”语音](supported-languages.md#text-to-speech)。

## <a name="make-a-request"></a>发出请求

在 POST 模式下发出文本转语音 HTTP 请求，请求正文中包含要说出的文本。 HTTP 请求正文的长度不超过 1024 个字符。 请求必须具有以下标头： 

标头|值|注释
-|-|-
|`Content-Type` | `application/ssml+xml` | 输入文本格式。
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | 输出音频格式。
|`User-Agent`   |应用程序名称 | 应用程序名称必填，且长度不得超过 255 个字符。
| `Authorization`   | 通过向令牌服务提供订阅密钥获得的授权令牌。 每个令牌的有效期为 10 分钟。 详情请参阅 [REST API：身份验证](rest-apis.md#authentication)。

> [!NOTE]
> 如果所选语音和输出格式具有不同的比特率，则根据需要对音频重新采样。 24khz 语音不支持 `audio-16khz-16kbps-mono-siren` 和 `riff-16khz-16kbps-mono-siren` 输出格式。 

示例请求显示如下。

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

状态为 200 的响应正文包含指定输出格式的音频。

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

如果发生错误，则使用以下状态代码。 错误的响应正文还包含问题说明。

|代码|Description|问题|
|-|-|-|
400 |错误的请求 |必需参数缺失、为空或为 null。 或者，传递给必需参数或可选参数的值无效。 常见问题是标头太长。
401|未授权 |请求未经授权。 请检查确保订阅密钥或令牌有效。
413|请求实体太大|SSML 输入超过了 1024 个字符。
|502|错误的网关    | 网络或服务器端问题。 也可能表示标头无效。

有关文本转语音 REST API 的详细信息，请参阅 [REST API](rest-apis.md#text-to-speech)。

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [在 C++ 中识别语音](quickstart-cpp-windows.md)
- [在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
- [在 Java 中识别语音](quickstart-java-android.md)
