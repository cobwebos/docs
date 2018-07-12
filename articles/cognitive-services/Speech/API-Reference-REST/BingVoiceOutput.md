---
title: Microsoft 语音服务的文本转语音 API | Microsoft Docs
description: 使用文本转语音 API，以各种语音和语言提供实时文本转语音转换
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 03/16/2017
ms.author: priyar
ms.openlocfilehash: 4b633cefa37c11511a8171d5a7f61b03dfaa4466
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365773"
---
# <a name="bing-text-to-speech-api"></a>必应文本转语音 API

## <a name="Introduction"></a>介绍

使用必应文本转语音 API 时，应用程序可以将 HTTP 请求发送到云服务器，在这里，文本立即合成为人工语音，并作为音频文件返回。 该 API 可用于许多不同的上下文，以各种不同的语音和语言提供实时的文本转语音转换。

## <a name="VoiceSynReq"></a>语音合成请求

### <a name="Subscription"></a>授权令牌

每个语音合成请求都需要 JSON Web 令牌 (JWT) 访问令牌。 JWT 访问令牌在语音请求头中传递。 令牌的到期时间为 10 分钟。 有关订阅和获取用于检索有效 JWT 访问令牌的 API 密钥的信息，请参阅[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)。

API 密钥将传递给令牌服务。 例如：

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

令牌访问所需的标头信息如下所示。

名称| 格式 | 说明
----|----|----
Ocp-Apim-Subscription-Key | ASCII | 订阅密钥

令牌服务将 JWT 访问令牌返回为 `text/plain`。 然后将 JWT 作为 `Base64 access_token` 传递给语音终结点，作为以 `Bearer` 为前缀字符串的授权标头。 例如：

`Authorization: Bearer [Base64 access_token]`

客户端必须使用以下终结点来访问文本转语音服务：

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>在如前所述使用订阅密钥获取访问令牌之前，此链接会生成 `403 Forbidden` 响应错误。

### <a name="Http"></a>HTTP 标头

下表展示了用于语音合成请求的 HTTP 标头。

标头 |值 |注释
----|----|----
Content-Type | application/ssml+xml | 输入内容类型。
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | 输出音频格式。
X-Search-AppId | GUID（仅限十六进制，无短划线） | 唯一标识客户端应用程序的 ID。 它可以是应用的 Store ID。 如果 Store ID 不可用，也可以是用户为应用程序生成的 ID。
X-Search-ClientID | GUID（仅限十六进制，无短划线） | 唯一标识每个安装的应用程序实例的 ID。
User-Agent | 应用程序名称 | 应用程序名称必填，且长度不得超过 255 个字符。
授权 | 授权令牌 |  请参阅<a href="#Subscription">授权令牌</a>部分。

### <a name="InputParam"></a>输入参数

使用 HTTP POST 调用请求必应文本转语音 API。 标头在上一部分中指定。 正文包含语音合成标记语言 (SSML) 输入，表示要合成的文本。 有关用于控制语音各方面（例如说话人的语言和性别）的标记的说明，请参阅 [SSML W3C 规范](http://www.w3.org/TR/speech-synthesis/)。

>[!NOTE]
>支持的最大 SSML 输入大小为 1,024 个字符，其中包括所有标记。

###  <a name="SampleVoiceOR"></a>示例：语音输出请求

语音输出请求示例如下所示：

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>语音输出响应

必应文本转语音 API 使用 HTTP POST 将音频发送回客户端。 API 响应包含音频流和编解码器，并且它与请求的输出格式匹配。 为给定请求返回的音频不得超过 15 秒。

### <a name="SuccessfulRecResponse"></a>示例：成功的合成响应

以下代码是对成功的语音合成请求的 JSON 响应示例。 该代码的注释和格式仅用于此示例，在实际响应中将被省略。

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>示例：合成失败

以下示例代码展示了对语音合成查询失败的 JSON 响应：

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>错误响应

错误 | 说明
----|----
HTTP/400 错误的请求 | 必需参数缺失、为空或为 null，或者传递给必需参数或可选参数的值无效。 获得“无效”响应的一个原因是正在传递一个超过允许长度的字符串值。 对有问题的参数的简要说明包含在内。
HTTP/401 未授权 | 未对请求授权。
HTTP/413 RequestEntityTooLarge  | SSML 输入超过支持的大小。
HTTP/502 BadGateway | 存在与网络相关的问题或服务器端问题。

错误响应示例如下所示：

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>通过 SSML 更改语音输出

Microsoft 文本转语音 API 支持 W3C [语音合成标记语言 (SSML) 版本 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) 中定义的 SSML 1.0。 此部分展示使用 SSML 标记更改生成的语音输出的某些特征（如语速、发音等）的示例。

1. 添加休息时间

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. 更改语速

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. 发音

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. 更改音量

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. 更改音调

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. 更改韵律轮廓

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> 请注意，音频数据必须是采用以下格式的 8k 或 16k wav 文件：**CRC 代码** (CRC-32)：4 个字节 (DWORD)，有效范围为 0x00000000 ~ 0xFFFFFFFF；**音频格式标志**：4 个字节 (DWORD)，有效范围为 0x00000000 ~ 0xFFFFFFFF；**示例计数**：4 个字节 (DWORD)，有效范围为 0x00000000 ~ 0x7FFFFFFF；**二进制主体的大小**：4 个字节 (DWORD)，有效范围为 0x00000000 ~ 0x7FFFFFFF；**二进制主体**：n 个字节。

## <a name="SampleApp"></a>示例应用程序

有关实现详细信息，请参阅 [Visual C#.NET 文本转语音示例应用程序](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs)。

## <a name="SupLocales"></a>支持的区域设置和语音字体

下表列出了一些受支持的区域设置和相关语音字体。

区域设置 | 性别 | 服务名称映射
---------|--------|------------
ar-EG* | 女 | “Microsoft 服务器语音的文本转语音（ar-EG，Hoda）”
ar-SA | 男 | “Microsoft 服务器语音的文本转语音（ar-SA，Naayf）”
bg-BG | 男 | “Microsoft 服务器语音的文本转语音（bg-BG，Ivan）”
ca-ES | 女 | “Microsoft 服务器语音的文本转语音（ca-ES，HerenaRUS）”
cs-CZ | 男 | “Microsoft 服务器语音的文本转语音（cs-CZ，Jakub）”
da-DK | 女 | “Microsoft 服务器语音的文本转语音（da-DK，HelleRUS）”
de-AT | 男 | “Microsoft 服务器语音的文本转语音（de-AT，Michael）”
de-CH | 男 | “Microsoft 服务器语音的文本转语音（de-CH，Karsten）”
de-DE | 女 | “Microsoft 服务器语音的文本转语音（de-DE，Hedda）”
de-DE | 女 | “Microsoft 服务器语音的文本转语音（de-DE，HeddaRUS）”
de-DE | 男 | “Microsoft 服务器语音的文本转语音（de-DE，Stefan，Apollo）”
el-GR | 男 | “Microsoft 服务器语音的文本转语音（el-GR，Stefanos）”
en-AU | 女 | “Microsoft 服务器语音的文本转语音（en-AU，Catherine）”
en-AU | 女 | “Microsoft 服务器语音的文本转语音（en-AU，HayleyRUS）”
en-CA | 女 | “Microsoft 服务器语音的文本转语音（en-CA，Linda）”
en-CA | 女 | “Microsoft 服务器语音的文本转语音（en-CA，HeatherRUS）”
en-GB | 女 | “Microsoft 服务器语音的文本转语音（en-GB，Susan，Apollo）”
en-GB | 女 | “Microsoft 服务器语音的文本转语音（en-GB，HazelRUS）”
en-GB | 男 | “Microsoft 服务器语音的文本转语音（en-GB，George，Apollo）”
en-IE | 男 | “Microsoft 服务器语音的文本转语音（en-IE，Sean）”
en-IN | 女 | “Microsoft 服务器语音的文本转语音（en-IN，Heera，Apollo）”
en-IN | 女 | “Microsoft 服务器语音的文本转语音（en-IN，PriyaRUS）”
en-IN | 男 | “Microsoft 服务器语音的文本转语音（en-IN，Ravi，Apollo）”
en-US | 女 | “Microsoft 服务器语音的文本转语音（en-US，ZiraRUS）”
en-US | 女 | “Microsoft 服务器语音的文本转语音（en-US，JessaRUS）”
en-US | 男 | “Microsoft 服务器语音的文本转语音（en-US，BenjaminRUS）”
es-ES | 女 | “Microsoft 服务器语音的文本转语音（es-ES，Laura，Apollo）”
es-ES | 女 | “Microsoft 服务器语音的文本转语音（es-ES，HelenaRUS）”
es-ES | 男 | “Microsoft 服务器语音的文本转语音（es-ES，Pablo，Apollo）”
es-MX | 女 | “Microsoft 服务器语音的文本转语音（es-MX，HildaRUS）”
es-MX | 男 | “Microsoft 服务器语音的文本转语音（es-MX，Raul，Apollo）”
fi-FI | 女 | “Microsoft 服务器语音的文本转语音（fi-FI，HeidiRUS）”
fr-CA | 女 | “Microsoft 服务器语音的文本转语音（fr-CA，Caroline）”
fr-CA | 女 | “Microsoft 服务器语音的文本转语音（fr-CA，HarmonieRUS）”
fr-CH | 男 | “Microsoft 服务器语音的文本转语音（fr-CH，Guillaume）”
fr-FR | 女 | “Microsoft 服务器语音的文本转语音（fr-FR，Julie，Apollo）”
fr-FR | 女 | “Microsoft 服务器语音的文本转语音（fr-FR，HortenseRUS）”
fr-FR | 男 | “Microsoft 服务器语音的文本转语音（fr-FR，Paul，Apollo）”
he-IL| 男| “Microsoft 服务器语音的文本转语音（he-IL，Asaf）”
hi-IN | 女 | “Microsoft 服务器语音的文本转语音（hi-IN，Kalpana，Apollo）”
hi-IN | 女 | “Microsoft 服务器语音的文本转语音（hi-IN，Kalpana）”
hi-IN | 男 | “Microsoft 服务器语音的文本转语音（hi-IN，Hemant）”
hr-HR | 男 | “Microsoft 服务器语音的文本转语音（hr-HR，Matej）”
hu-HU | 男 | “Microsoft 服务器语音的文本转语音（hu-HU，Szabolcs）”
id-ID | 男 | “Microsoft 服务器语音的文本转语音（id-ID，Andika）”
it-IT | 男 | “Microsoft 服务器语音的文本转语音（it-IT，Cosimo，Apoll）”
ja-JP | 女 | “Microsoft 服务器语音的文本转语音（ja-JP，Ayumi，Apollo）”
ja-JP | 男 | “Microsoft 服务器语音的文本转语音（ja-JP，Ichiro，Apollo）”
ja-JP | 女 | “Microsoft 服务器语音的文本转语音（ja-JP，HarukaRUS）”
ja-JP | 女 | “Microsoft 服务器语音的文本转语音（ja-JP，LuciaRUS）”
ja-JP | 男 | “Microsoft 服务器语音的文本转语音（ja-JP，EkaterinaRUS）”
ko-KR | 女 | “Microsoft 服务器语音的文本转语音（ko-KR，HeamiRUS）”
ms-MY | 男 | “Microsoft 服务器语音的文本转语音（ms-MY，Rizwan）”
nb-NO | 女 | “Microsoft 服务器语音的文本转语音（nb-NO，HuldaRUS）”
nl-NL | 女 | “Microsoft 服务器语音的文本转语音（nl-NL，HannaRUS）”
pl-PL | 女 | “Microsoft 服务器语音的文本转语音（pl-PL，PaulinaRUS）”
pt-BR | 女 | “Microsoft 服务器语音的文本转语音（pt-BR，HeloisaRUS）”
pt-BR | 男 | “Microsoft 服务器语音的文本转语音（pt-BR，Daniel，Apollo）”
pt-PT | 女 | “Microsoft 服务器语音的文本转语音（pt-PT，HeliaRUS）”
ro-RO | 男 | “Microsoft 服务器语音的文本转语音（ro-RO，Andrei）”
ru-RU | 女 | “Microsoft 服务器语音的文本转语音（ru-RU，Irina，Apollo）”
ru-RU | 男 | “Microsoft 服务器语音的文本转语音（ru-RU，Pavel，Apollo）”
sk-SK | 男 | “Microsoft 服务器语音的文本转语音（sk-SK，Filip）”
sl-SI | 男 | “Microsoft 服务器语音的文本转语音（sl-SI，Lado）”
sv-SE | 女 | “Microsoft 服务器语音的文本转语音（sv-SE，HedvigRUS）”
ta-IN | 男 | “Microsoft 服务器语音的文本转语音（ta-IN，Valluvar）”
th-TH | 男 | “Microsoft 服务器语音的文本转语音（th-TH，Pattara）”
tr-TR | 女 | “Microsoft 服务器语音的文本转语音（tr-TR，SedaRUS）”
vi-VN | 男 | “Microsoft 服务器语音的文本转语音（vi-VN，An）”
zh-CN | 女 | “Microsoft 服务器语音的文本转语音（zh-CN，HuihuiRUS）”
zh-CN | 女 | “Microsoft 服务器语音的文本转语音（zh-CN，Yaoyao，Apollo）”
zh-CN | 男 | “Microsoft 服务器语音的文本转语音（zh-CN，Kangkang，Apollo）”
zh-HK | 女 | “Microsoft 服务器语音的文本转语音（zh-HK，Tracy，Apollo）”
zh-HK | 女 | “Microsoft 服务器语音的文本转语音（zh-HK，TracyRUS）”
zh-HK | 男 | “Microsoft 服务器语音的文本转语音（zh-HK，Danny，Apollo）”
zh-TW | 女 | “Microsoft 服务器语音的文本转语音（zh-TW，Yating，Apollo）”
zh-TW | 女 | “Microsoft 服务器语音的文本转语音（zh-TW，HanHanRUS）”
zh-TW | 男 | “Microsoft 服务器语音的文本转语音（zh-TW，Zhiwei，Apollo）”
 *ar-EG 支持现代标准阿拉伯语 (MSA)。

> [!NOTE]
> 请注意，为了优化必应语音 API 的功能，以前的服务名称 **Microsoft 服务器语音的文本转语音（cs-CZ，Vit）** 和 **Microsoft 服务器语音的文本转语音（en-IE，Shaun）** 将在 2018 年 3 月 31 日之后被弃用。 请使用更新的名称更新代码。

## <a name="TrouNSupport"></a>故障排除和支持

将所有问题发布到[必应语音服务](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) MSDN 论坛。 包括完整的详细信息，例如：

* 完整请求字符串的示例。
* 失败请求的完整输出（如果适用），其中包括日志 ID。
* 失败请求所占的百分比。
