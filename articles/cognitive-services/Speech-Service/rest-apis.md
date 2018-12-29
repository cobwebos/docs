---
title: 语音服务 REST API - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音转文本和文本转语音 REST API。 本文介绍授权选项、查询选项，以及如何构建请求和接收响应。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0b38c61f4fe884137204cba6d99d5e383b3259a0
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338884"
---
# <a name="speech-service-rest-apis"></a>语音服务 REST API

作为 [Speech SDK](speech-sdk.md) 的替代服务，语音服务可让你通过一组 REST API 进行语音转文本和文本转语音转换。 每个可访问的终结点都与某个区域相关联。 应用程序需要所用终结点的订阅密钥。

在使用 REST API 之前，应了解：
* 使用 REST API 的语音转文本请求只能包含 10 秒的录制音频。
* 语音转文本 REST API 仅返回最终结果。 不提供部分结果。
* 文本转语音 REST API 需要授权标头。 这意味着，需要完成令牌交换才能访问该服务。 有关详细信息，请参阅[身份验证](#authentication)。

## <a name="authentication"></a>身份验证

向语音转文本或文本转语音 REST API 发出的每个请求都需要授权标头。 下表列出了每个服务支持的标头：

| 支持的授权标头 | 语音转文本 | 文本转语音 |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | 是 | 否 |
| Authorization:持有者 | 是 | 是 |

使用 `Ocp-Apim-Subscription-Key` 标头时，只需提供订阅密钥。 例如：

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

使用 `Authorization: Bearer` 标头时，需要向 `issueToken` 终结点发出请求。 在此请求中，交换有效期为 10 分钟的访问令牌的订阅密钥。 下面的几个部分将介绍如何获取令牌、使用令牌和刷新令牌。

### <a name="how-to-get-an-access-token"></a>如何获取访问令牌

若要获取访问令牌，需使用 `Ocp-Apim-Subscription-Key` 和订阅密钥向 `issueToken` 终结点发出请求。

支持以下区域和终结点：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

使用这些示例创建访问令牌请求。

#### <a name="http-sample"></a>HTTP 示例

此示例是获取令牌的简单 HTTP 请求。 请将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 如果订阅不在美国西部区域，请将 `Host` 标头替换为所在区域的主机名。

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

响应正文包含 Java Web 令牌 (JWT) 格式的访问令牌。

#### <a name="powershell-sample"></a>PowerShell 示例

此示例是获取访问令牌的简单 PowerShell 脚本。 请将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 请务必使用与订阅匹配的正确区域终结点。 此示例目前设置为“美国西部”。

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL 示例

cURL 是 Linux（及面向 Linux 的 Windows 子系统）中提供的一种命令行工具。 此 cURL 命令演示如何获取访问令牌。 请将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 请务必使用与订阅匹配的正确区域终结点。 此示例目前设置为“美国西部”。

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# 示例

此 C# 类演示如何获取访问令牌。 实例化该类时，请传递语音服务订阅密钥。 如果订阅不在美国西部区域，请更改 `FetchTokenUri` 的值，以便与订阅的区域相匹配。

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>如何使用访问令牌

应将访问令牌作为 `Authorization: Bearer <TOKEN>` 标头发送到服务。 每个访问令牌的有效期为 10 分钟。 随时可以获取新令牌，但是，为了最大限度地减少流量和延迟，我们建议使用同一令牌 9 分钟。

下面是向文本转语音 REST API 发出的示例 HTTP 请求：

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>如何使用 C# 续订访问令牌

此 C# 代码是前面所述类的直接替换项。 `Authentication` 类使用计时器每隔 9 分钟自动获取新的访问令牌。 此方法可确保在程序运行期间始终具备有效令牌。

> [!NOTE]
> 如果不使用计时器，可以存储上次获取令牌时的时间戳。 然后，可以在令牌即将过期时才请求新令牌。 这样可以避免在不必要的情况下请求新令牌，此方法可能更适合用于不常发出语音请求的程序。

如上述一样，请确保 `FetchTokenUri` 值与订阅区域匹配。 实例化该类时，请传递订阅密钥。

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>语音转文本 API

语音转文本 REST API 仅支持简短话语。 请求可以包含最长 10 秒、总持续时间为 14 秒的音频。 REST API 只返回最终结果，不返回部分或中间结果。

如果必须为应用程序发送更长的音频，请考虑使用[语音 SDK](speech-sdk.md) 或[批处理脚本](batch-transcription.md)。

### <a name="regions-and-endpoints"></a>区域和终结点

使用 REST API 的语音转文本听录支持以下区域。 请务必选择与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>查询参数

可将以下参数包含在 REST 请求的查询字符串中。

| 参数 | Description | 必需/可选 |
|-----------|-------------|---------------------|
| `language` | 标识所要识别的口语。 请参阅[支持的语言](language-support.md#speech-to-text)。 | 必选 |
| `format` | 指定结果格式。 接受的值为 `simple` 和 `detailed`。 简单结果包括 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 详细响应包括多个具有置信度值的结果，以及四种不同的表示形式。 默认设置是 `simple`。 | 可选 |
| `profanity` | 指定如何处理识别结果中的不雅内容。 接受的值为 `masked`（将亵渎内容替换为星号）、`removed`（删除结果中的所有亵渎内容）或 `raw`（包含结果中的亵渎内容）。 默认设置是 `masked`。 | 可选 |

### <a name="request-headers"></a>请求标头

下表列出了语音转文本请求的必需和可选标头。

|标头| Description | 必需/可选 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 语音服务订阅密钥。 | 此标头或 `Authorization` 是必需的。 |
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关详细信息，请参阅[身份验证](#authentication)。 | 此标头或 `Ocp-Apim-Subscription-Key` 是必需的。 |
| `Content-type` | 描述所提供音频数据的格式和编解码器。 接受的值为 `audio/wav; codec=audio/pcm; samplerate=16000` 和 `audio/ogg; codec=audio/pcm; samplerate=16000`。 | 必选 |
| `Transfer-Encoding` | 指定要发送分块的音频数据，而不是单个文件。 仅当要对音频数据进行分块时才使用此标头。 | 可选 |
| `Expect` | 如果使用分块传输，则发送 `Expect: 100-continue`。 语音服务将确认初始请求并等待附加的数据。| 如果发送分块的音频数据，则是必需的。 |
| `Accept` | 如果提供此标头，则值必须是 `application/json`。 语音服务以 JSON 格式提供结果。 如果未指定默认值，则某些 Web 请求框架会提供不兼容的默认值，因此，最佳做法是始终包含 `Accept`。 | 可选，但建议提供。 |

### <a name="audio-formats"></a>音频格式

在 HTTP `POST` 请求的正文中发送音频。 它必须采用下表中的格式之一：

| 格式 | 编解码器 | Bitrate | 采样率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位 | 16 kHz，单声道 |
| OGG | OPUS | 16 位 | 16 kHz，单声道 |

>[!NOTE]
>语音服务中的 REST API 和 WebSocket 支持上述格式。 [语音 SDK](speech-sdk.md) 目前仅支持使用 PCM 编解码器的 WAV 格式。

### <a name="sample-request"></a>示例请求

这是一个典型的 HTTP 请求。 以下示例包括主机名和必需的标头。 必须注意，服务同时预期提供音频数据，但此示例未包括这些数据。 如前所述，建议进行分块，但不是非要这样做。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | Description | 可能的原因 |
|------------------|-------------|-----------------|
| 100 | 继续 | 已接受初始请求。 继续发送剩余的数据。 （与分块传输配合使用。） |
| 200 | OK | 请求成功；响应正文是一个 JSON 对象。 |
| 400 | 错误的请求 | 语言代码未提供或不是支持的语言；音频文件无效。 |
| 401 | 未授权 | 指定区域中的订阅密钥或授权令牌无效，或终结点无效。 |
| 403 | 禁止 | 缺少订阅密钥或授权令牌。 |

### <a name="chunked-transfer"></a>分块传输

分块传输 (`Transfer-Encoding: chunked`) 有助于降低识别延迟，因为它允许语音服务在传输期间开始处理音频文件。 REST API 不提供部分结果或临时结果。 此选项专门用于改善响应能力。

此代码示例演示如何以块的形式发送音频。 只有第一个区块应该包含音频文件的标头。 `request` 是连接到相应 REST 终结点的 HTTPWebRequest 对象。 `audioFile` 是音频文件在磁盘上的路径。

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codec=""audio/pcm""; samplerate=16000";
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

### <a name="response-parameters"></a>响应参数

结果以 JSON 格式提供。 `simple` 格式包含以下顶级字段。

| 参数 | Description  |
|-----------|--------------|
|`RecognitionStatus`|状态，例如 `Success` 表示成功识别。 请参阅下表。|
|`DisplayText`|经过大小写转换、添加标点、执行反向文本规范化（将口头文本转换为短形式，例如，200 表示“two hundred”，或“Dr.Smith”表示“doctor smith”）和屏蔽亵渎内容之后的识别文本。 仅在成功时提供。|
|`Offset`|在音频流中开始识别语音的时间（以 100 纳秒为单位）。|
|`Duration`|在音频流中识别语音的持续时间（以 100 纳秒为单位）。|

`RecognitionStatus` 字段可包含以下值：

| 状态 | Description |
|--------|-------------|
| `Success` | 识别成功并且存在 `DisplayText` 字段。 |
| `NoMatch` | 在音频流中检测到语音，但没有匹配目标语言的字词。 通常表示识别语言不同于讲话用户所用的语言。 |
| `InitialSilenceTimeout` | 音频流的开始仅包含静音，并且服务在等待语音时超时。 |
| `BabbleTimeout` | 音频流的开始仅包含噪音，并且服务在等待语音时超时。 |
| `Error` | 识别服务遇到内部错误，无法继续。 如果可能，请重试。 |

> [!NOTE]
> 如果音频仅包含亵渎内容，并且 `profanity` 查询参数设置为 `remove`，则服务不会返回语音结果。

`detailed` 格式包含的数据与 `simple` 格式相同，另外还包含 `NBest`，这是相同语音识别结果的替代解释。 这些结果从最有可能到最不可能进行排名。第一个条目与主要识别结果相同。  使用 `detailed` 格式时，将以 `Display` 形式为 `NBest` 列表中的每条结果提供 `DisplayText`。

`NBest` 列表中的每个对象包括：

| 参数 | Description |
|-----------|-------------|
| `Confidence` | 条目的置信度评分，从 0.0（完全不可信）到 1.0（完全可信） |
| `Lexical` | 已识别文本的词法形式：识别的实际单词。 |
| `ITN` | 已识别文本的反向文本规范化（“规范”）形式，已应用电话号码、数字、缩写（“doctor smith”缩写为“dr smith”）和其他转换。 |
| `MaskedITN` | 可根据请求提供应用了亵渎内容屏蔽的 ITN 形式。 |
| `Display` | 已识别文本的显示形式，其中添加了标点符号和大小写形式。 此参数与将格式设置为 `simple` 时提供的 `DisplayText` 相同。 |

### <a name="sample-responses"></a>示例响应

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

## <a name="text-to-speech-api"></a>文本转语音 API

文本转语音 REST API 支持神经和标准文本转语音，每种语音支持区域设置标识的特定语言和方言。

* 有关语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。
* 有关区域可用性的信息，请参阅[区域](regions.md#text-to-speech)。

### <a name="request-headers"></a>请求标头

下表列出了语音转文本请求的必需和可选标头。

| 标头 | Description | 必需/可选 |
|--------|-------------|---------------------|
| `Authorization` | 前面带有单词 `Bearer` 的授权令牌。 有关其他信息，请参阅[身份验证](#authentication)。 | 必选 |
| `Content-Type` | 指定所提供的文本的内容类型。 接受的值：`application/ssml+xml`。 | 必选 |
| `X-Microsoft-OutputFormat` | 指定音频输出格式。 有关接受值的完整列表，请参阅[音频输出](#audio-outputs)。 | 必选 |
| `User-Agent` | 应用程序名称。 此名称必须小于 255 个字符。 | 必选 |

### <a name="audio-outputs"></a>音频输出

这是在每个请求中作为 `X-Microsoft-OutputFormat` 标头发送的受支持音频格式的列表。 每种格式合并了比特率和编码类型。 语音服务支持 24 KHz 和 16 KHz 音频输出。

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-mulaw` | `riff-16khz-16bit-mono-pcm` |
| `audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3` |
| `audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm` |
| `riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3` |
| `audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3` |

> [!NOTE]
> 如果所选语音和输出格式具有不同的比特率，则根据需要对音频重新采样。 但是，24khz 语音不支持 `audio-16khz-16kbps-mono-siren` 和 `riff-16khz-16kbps-mono-siren` 输出格式。

### <a name="request-body"></a>请求正文

作为 HTTP `POST` 请求正文发送的文本。 可以是纯文本（ASCII 或 UTF-8），或[语音合成标记语言](speech-synthesis-markup.md) (SSML) 格式 (UTF-8)。 纯文本请求使用语音服务的默认语音和语言。 使用 SSML 可以指定语音和语言。

### <a name="sample-request"></a>示例请求

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

### <a name="http-status-codes"></a>HTTP 状态代码

每个响应的 HTTP 状态代码指示成功或一般错误。

| HTTP 状态代码 | Description | 可能的原因 |
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
