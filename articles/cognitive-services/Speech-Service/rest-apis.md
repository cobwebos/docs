---
title: 语音服务 REST API
description: 语音服务 REST API 的参考。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 6758cd658daf75beeea93bf9c719508cd271c8be
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032421"
---
# <a name="speech-service-rest-apis"></a>语音服务 REST API

Azure 认知服务统一语音服务的 REST API 与[必应语音 API](https://docs.microsoft.com/azure/cognitive-services/Speech)提供的 API 类似。 该终结点与必应语音服务使用的终结点不同。 可以使用区域终结点，但必须使用对应于所用终结点的订阅密钥。

## <a name="speech-to-text"></a>语音转文本

下表显示了语音转文本 REST API 的终结点。 使用与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> 如果已自定义声学/语言模型或发音，请改用自定义终结点。

此 API 仅支持短话语。 请求最多可包含 10 秒的音频，总共持续 14 秒。 REST API 仅返回最终结果，不返回部分或中间结果。 语音服务还提供一个可以听录较长音频的[批处理听录](batch-transcription.md) API。

### <a name="query-parameters"></a>查询参数

可将以下参数包含在 REST 请求的查询字符串中。

|参数名称|必需/可选|含义|
|-|-|-|
|`language`|必选|要识别的语言的标识符。 请参阅[支持的语言](supported-languages.md#speech-to-text)。|
|`format`|可选<br>默认值：`simple`|结果格式，`simple` 或 `detailed` 简单结果包括 `RecognitionStatus`、`DisplayText`、`Offset` 和持续时间。 详细结果包括多个具有置信度值的候选项，以及四种不同的表示形式。|
|`profanity`|可选<br>默认值：`masked`|如何处理识别结果中的亵渎内容。 可以是 `masked`（将亵渎内容替换为星号）、`removed`（删除所有亵渎内容）或 `raw`（包含亵渎内容）。

### <a name="request-headers"></a>请求标头

在 HTTP 请求标头中发送以下字段。

|标头|含义|
|------|-------|
|`Ocp-Apim-Subscription-Key`|语音服务订阅密钥。 必须提供此标头或 `Authorization`。|
|`Authorization`|前面带有单词 `Bearer` 的授权令牌。 必须提供此标头或 `Ocp-Apim-Subscription-Key`。 请参阅[身份验证](#authentication)。|
|`Content-type`|描述音频数据的格式和编解码器。 目前，此值必须是 `audio/wav; codec=audio/pcm; samplerate=16000`。|
|`Transfer-Encoding`|可选。 如果提供，则必须是 `chunked`，以便将音频数据发送到多个小区块而不是单个文件。|
|`Expect`|如果使用分块传输，则发送 `Expect: 100-continue`。 语音服务将确认初始请求并等待附加的数据。|
|`Accept`|可选。 如果提供，则必须包含 `application/json`，因为语音服务以 JSON 格式提供结果。 （如果未指定默认值，则某些 Web 请求框架会提供不兼容的默认值，因此，最佳做法是始终包含 `Accept`。）|

### <a name="audio-format"></a>音频格式

在 HTTP `PUT` 请求的正文中发送音频。 音频应采用 PCM 单声道（单音）16 位 WAV 格式，采样率为 16 KHz。

### <a name="chunked-transfer"></a>分块传输

分块传输 (`Transfer-Encoding: chunked`) 有助于降低识别延迟，因为它允许语音服务在传输期间开始处理音频文件。 REST API 不提供部分结果或临时结果。 此选项专门用于改善响应能力。

以下代码演示如何分块发送音频。 `request` 是连接到相应 REST 终结点的 HTTPWebRequest 对象。 `audioFile` 是音频文件在磁盘上的路径。

```csharp
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

### <a name="example-request"></a>示例请求

下面是一个典型的请求。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>HTTP 状态

响应的 HTTP 状态指示成功或一般错误状态。

HTTP 代码|含义|可能的原因
-|-|-|
100|继续|已接受初始请求。 继续发送剩余的数据。 （与分块传输配合使用。）
200|OK|请求成功；响应正文是一个 JSON 对象。
400|错误的请求|语言代码未提供或不是支持的语言；音频文件无效。
401|未授权|指定区域中的订阅密钥或授权令牌无效，或终结点无效。
403|禁止|缺少订阅密钥或授权令牌。

### <a name="json-response"></a>JSON 响应

结果以 JSON 格式返回。 `simple` 格式仅包括以下顶级字段。

|字段名称|内容|
|-|-|
|`RecognitionStatus`|状态，例如 `Success` 表示成功识别。 请参阅下表。|
|`DisplayText`|经过大小写转换、添加标点、执行反向文本规范化（将口头文本转换为短形式，例如，200 表示“two hundred”，或“Dr.Smith”表示“doctor smith”）和屏蔽亵渎内容之后的识别文本。 仅在成功时提供。|
|`Offset`|在音频流中开始识别语音的时间（以 100 纳秒为单位）。|
|`Duration`|在音频流中识别语音的持续时间（以 100 纳秒为单位）。|

`RecognitionStatus` 字段可能包含以下值。

|状态值|Description
|-|-|
| `Success` | 识别成功并且存在 DisplayText 字段。 |
| `NoMatch` | 在音频流中检测到语音，但没有匹配目标语言的字词。 通常表示识别语言不同于讲话用户所用的语言。 |
| `InitialSilenceTimeout` | 音频流的开始仅包含静音，并且服务在等待语音时超时。 |
| `BabbleTimeout` | 音频流的开始仅包含噪音，并且服务在等待语音时超时。 |
| `Error` | 识别服务遇到内部错误，无法继续。 如果可能，请重试。 |

> [!NOTE]
> 如果音频仅包含亵渎内容，并且 `profanity` 查询参数设置为 `remove`，则服务不会返回语音结果。 


`detailed` 格式包含 `simple` 格式所包含的相同字段，并包含 `NBest` 字段。 `NBest` 字段是相同语音的备选解释列表，从最有可能到最不可能进行排名。 第一个条目与主要识别结果相同。 每个条目包含以下字段：

|字段名称|内容|
|-|-|
|`Confidence`|条目的置信度评分，从 0.0（完全不可信）到 1.0（完全可信）
|`Lexical`|已识别文本的词法形式：识别的实际单词。
|`ITN`|已识别文本的反向文本规范化（“规范”）形式，已应用电话号码、数字、缩写（“doctor smith”缩写为“dr smith”）和其他转换。
|`MaskedITN`| 可根据请求提供应用了亵渎内容屏蔽的 ITN 形式。
|`Display`| 已识别文本的显示形式，其中添加了标点符号和大小写形式。 与顶级结果中的 `DisplayText` 相同。

### <a name="sample-responses"></a>示例响应

下面是 `simple` 识别的典型响应。

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

下面是 `detailed` 识别的典型响应。

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

## <a name="text-to-speech"></a>文本到语音转换

下面是语音服务文本转语音 API 的 REST 终结点。 请使用与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

> [!NOTE]
> 如果创建了自定义语音字体，请改用关联的自定义终结点。

语音服务支持 24-KHz 音频输出，此外还支持必应语音所支持的 16-Khz 输出。 在 `X-Microsoft-OutputFormat` HTTP 标头中可以使用四种 24-KHz 输出格式，支持两种 24-KHz 语音：`Jessa24kRUS` 和 `Guy24kRUS`。

区域设置 | 语言   | 性别 | 服务名称映射
-------|------------|--------|------------
en-US  | 美式英语 | 女 | “Microsoft 服务器语音的文本转语音（en-US，Jessa24kRUS）” 
en-US  | 美式英语 | 男   | “Microsoft 服务器语音的文本转语音（en-US，Guy24kRUS）”

[支持的语言](supported-languages.md#text-to-speech)中提供了可用语音的完整列表。

### <a name="request-headers"></a>请求标头

在 HTTP 请求标头中发送以下字段。

|标头|含义|
|------|-------|
|`Authorization`|前面带有单词 `Bearer` 的授权令牌。 必需。 请参阅[身份验证](#authentication)。|
|`Content-Type`|输入内容类型：`application/ssml+xml`。|
|`X-Microsoft-OutputFormat`|输出音频格式。 请参阅下表。|
|`User-Agent`|应用程序名称。 必需；长度必须小于 255 个字符。|

可用音频输出格式 (`X-Microsoft-OutputFormat`) 整合了比特率和编码。

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> 如果所选语音和输出格式具有不同的比特率，则根据需要对音频重新采样。 但是，24khz 语音不支持 `audio-16khz-16kbps-mono-siren` 和 `riff-16khz-16kbps-mono-siren` 输出格式。 

### <a name="request-body"></a>请求正文

要转换为语音的文本将作为 HTTP `POST` 请求的正文，以纯文本（ASCII 或 UTF-8）或[语音合成标记语言](speech-synthesis-markup.md) (SSML) 格式 (UTF-8) 发送。 纯文本请求使用服务的默认语音和语言。 发送 SSML 可使用不同的声音。

### <a name="sample-request"></a>示例请求

以下 HTTP 请求使用 SSML 正文来选择语音。 正文长度不得超过 1,000 个字符。

```xml
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

### <a name="http-response"></a>HTTP 响应

响应的 HTTP 状态指示成功或一般错误状态。

HTTP 代码|含义|可能的原因
-|-|-|
200|OK|请求成功；响应正文是一个音频文件。
400 |错误的请求 |必需参数缺失、为空或为 null。 或者，传递给必需参数或可选参数的值无效。 常见问题是标头太长。
401|未授权 |请求未经授权。 确保订阅密钥或令牌有效并在正确的区域中。
413|请求实体太大|SSML 输入超过了 1024 个字符。
|502|错误的网关    | 网络或服务器端问题。 也可能表示标头无效。

如果 HTTP 状态为 `200 OK`，则响应正文包含采用所请求格式的音频文件。 可以一边传输一边播放此文件，或者将其保存到缓冲区或文件中，以便日后播放或用于其他目的。

## <a name="authentication"></a>身份验证

向语音服务的 REST API 发送请求需要提供订阅密钥或访问令牌。 一般情况下，最简单的方法是直接发送订阅密钥。 然后，语音服务将为你获取访问令牌。 为了尽量缩短响应时间，可以改用访问令牌。

若要获取令牌，请向区域语音服务 `issueToken` 终结点提供订阅密钥，如下表中所示。 请使用与订阅区域匹配的终结点。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

每个访问令牌的有效期为 10 分钟。 随时可以获取新令牌。 如果需要，可以在发送每个语音 REST API 请求之前的那一刻获取令牌。 为了最大限度地减少流量和延迟，我们建议将同一个令牌使用 9 分钟。

以下部分介绍如何获取令牌以及如何在请求中使用令牌。

### <a name="get-a-token-http"></a>获取令牌：HTTP

下面是一个获取令牌的示例 HTTP 请求。 将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 如果订阅不在美国西部区域，请将 `Host` 标头替换为所在区域的主机名。

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

对此请求的响应主体是 Java Web 令牌 (JWT) 格式的访问令牌。

### <a name="get-a-token-powershell"></a>获取令牌：PowerShell

以下 Windows PowerShell 脚本演示如何获取访问令牌。 将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 如果订阅不在美国西部区域，请相应地更改给定 URI 的主机名。

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

### <a name="get-a-token-curl"></a>获取令牌：cURL

cURL 是 Linux（及面向 Linux 的 Windows 子系统）中提供的一种命令行工具。 以下 cURL 命令演示如何访问令牌。 将 `YOUR_SUBSCRIPTION_KEY` 替换为语音服务订阅密钥。 如果订阅不在美国西部区域，请相应地更改给定 URI 的主机名。

> [!NOTE]
> 为方便阅读，该命令分行显示，但在 shell 提示符下，请独行输入。

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>获取令牌：C#

以下 C# 类演示如何访问令牌。 实例化该类时，请传递语音服务订阅密钥。 如果订阅不在美国西部区域，请相应地更改 `FetchTokenUri` 的主机名。

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

### <a name="use-a-token"></a>使用令牌

要在 REST API 请求中使用令牌，请在 `Authorization` 标头的 `Bearer` 单词后面提供该令牌。 下面是包含令牌的文本转语音 REST 请求示例。 请将 `YOUR_ACCESS_TOKEN` 替换为实际令牌。 在 `Host` 标头中使用正确的主机名。

```xml
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

### <a name="renew-authorization"></a>续订授权

授权令牌在 10 分钟后过期。 在令牌过期之前获取新令牌可以续订授权。 例如，可以在 9 分钟后获取新令牌。

以下 C# 代码是先前所述的类的直接替换项。 `Authentication` 类使用计时器每隔 9 分钟自动获取一个新的访问令牌。 此方法可确保在程序运行期间始终具备有效令牌。

> [!NOTE]
> 如果不使用计时器，可以存储上次获取令牌时的时间戳。 然后，可以在令牌即将过期时才请求新令牌。 这样可以避免在不必要的情况下请求新令牌，此方法可能更适合用于不常发出语音请求的程序。

如上述一样，请确保 `FetchTokenUri` 值与订阅区域匹配。 实例化该类时，请传递订阅密钥。

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)

