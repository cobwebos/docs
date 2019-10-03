---
title: 通过 REST 开始使用必应语音识别 API | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 使用 REST 访问 Microsoft 认知服务中的语音识别 API，将语音转换为文本。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965787"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>快速入门：使用必应语音识别 REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

通过基于云的必应语音服务，可以使用 REST API 将语音转换为文本以开发应用程序。

## <a name="prerequisites"></a>先决条件

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>订阅语音 API，并获得免费试用的订阅密钥

语音 API 是认知服务（之前称为 Project Oxford）的一部分。 你可以从[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页面获取订阅密钥免费试用。 选择语音 API 后，选择“获取 API 密钥”以获取密钥。 它将返回主密钥和辅助密钥。 两个密钥都绑定到相同的配额，因此你可以使用任一密钥。

> [!IMPORTANT]
>* 获取订阅密钥。 必须具有[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)才能访问 REST API。
>
>* 使用订阅密钥。 在以下 REST 示例中，将 YOUR_SUBSCRIPTION_KEY 替换为自己的订阅密钥。
>
>* 请参阅[身份验证](../how-to/how-to-authentication.md)页面，了解如何获取订阅密钥。

### <a name="prerecorded-audio-file"></a>预先录制的音频文件

在此示例中，我们使用录制的音频文件来说明如何使用 REST API。 说一个短句并录制一个音频文件。 例如，说“今天天气怎么样？” 或者“查找有趣的电影。” 语音识别 API 还支持外部麦克风输入。

> [!NOTE]
> 示例要求音频以 PCM 单声道、16 KHz 的 WAV 文件格式进行录制，。

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>生成识别请求，并将其发送到语音识别服务

语音识别的下一步是将 POST 请求发送到有正确请求标头和正文的语音 HTTP 终结点。

### <a name="service-uri"></a>服务 URI

基于[识别模式](../concepts.md#recognition-modes)和[识别语言](../concepts.md#recognition-languages)定义语音识别服务 URI：

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` 指定识别模式，且必须是以下值之一：`interactive`、`conversation` 或 `dictation`。 它是 URI 中所需的资源路径。 有关详细信息，请参阅[识别模式](../concepts.md#recognition-modes)。

`<LANGUAGE_TAG>` 是查询字符串中的必需参数。 它定义音频转换的目标语言：例如，`en-US` 表示英语（美国）。 有关详细信息，请参阅[识别语言](../concepts.md#recognition-languages)。

`<OUTPUT_FORMAT>` 是查询字符串中的可选参数。 允许的值为 `simple` 和 `detailed`。 默认情况下，该服务返回 `simple` 格式的结果。 有关详细信息，请参阅[输出格式](../concepts.md#output-format)。

下表列出了一些服务 URI 示例。

| 识别模式  | 语言 | 输出格式 | 服务 URI |
|---|---|---|---|
| `interactive` | pt-BR | 默认 | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | 详细 | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | 简单 | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> 仅在应用程序使用 REST API 调用语音识别服务时才需要服务 URI。 如果使用的是这些[客户端库](GetStartedClientLibraries.md)之一，则通常不需要知道使用的 URI。 客户端库可能使用不同的服务 URI，它们仅适用于特定客户端库。 有关详细信息，请参阅选定客户端库。

### <a name="request-headers"></a>请求头

必须在请求标头中设置以下字段：

- `Ocp-Apim-Subscription-Key`：每次调用服务时，都必须传递 `Ocp-Apim-Subscription-Key` 标头中的订阅密钥。 除了订阅密钥，语音服务还支持传递授权令牌。 有关详细信息，请参阅[身份验证](../How-to/how-to-authentication.md)。
- `Content-type`：`Content-type` 字段描述音频流的格式和编解码器。 目前，仅支持 WAV 文件和 PCM Mono 16000 编码。 此格式的 Content-type 值是 `audio/wav; codec=audio/pcm; samplerate=16000`。

`Transfer-Encoding` 字段为可选。 如果将此字段设置为 `chunked`，则可以将音频拆分为小区块。 有关详细信息，请参阅[分块传输](../How-to/how-to-chunked-transfer.md)。

下面是示例请求标头：

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>将请求发送到服务

下面的示例演示如何将语音识别请求发送到语音 REST 终结点。 其中使用了 `interactive` 识别模式。

> [!NOTE]
> 将 `YOUR_AUDIO_FILE` 替换为预先录制的音频文件的路径。 将 `YOUR_SUBSCRIPTION_KEY` 替换为自己的订阅密钥。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

该示例使用具有 bash 的 Linux 上的 curl。 如果未在平台上提供 curl，则可能需要安装它。 该示例也适用于 Windows 上的 Cygwin、Git Bash、zsh 和其他 shell。

> [!NOTE]
> 将 `YOUR_AUDIO_FILE` 替换为预先录制的音频文件的路径时，请保留音频文件名前面的 `@`，因为它表示 `--data-binary` 的值是文件名，而不是数据。

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
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

---

## <a name="process-the-speech-recognition-response"></a>处理语音识别响应

处理请求后，语音服务返回 JSON 格式的响应结果。

> [!NOTE]
> 如果前面的代码返回错误，请参阅[疑难解答](../troubleshooting.md)查找可能的原因。

以下代码片段演示关于如何读取流响应的示例。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

在此示例中，curl 直接以字符串形式返回响应消息。 如果想要以 JSON 格式显示，可以使用其他工具（例如 jq）。

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

下例是 JSON 格式的响应：

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>限制

REST API 有一些限制：

- 它仅支持最多 15 秒的音频流。
- 它不支持识别期间的中间结果。 用户仅收到最终识别结果。

若要删除这些限制，请使用语音[客户端库](GetStartedClientLibraries.md)。 或者，可以直接使用[语音 WebSocket 协议](../API-Reference-REST/websocketprotocol.md)。

## <a name="whats-next"></a>后续步骤

- 若要查看如何使用 C#、Java 等中的 REST API，请参阅这些[示例应用程序](../samples.md)。
- 若要找出并解决错误，请参阅[疑难解答](../troubleshooting.md)。
- 若要使用更多高级功能，请使用语音[客户端库](GetStartedClientLibraries.md)查看使用方法。

### <a name="license"></a>许可证

所有认知服务 SDK 和示例均获得 MIT 许可证的许可。 有关详细信息，请参阅[许可证](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)。
