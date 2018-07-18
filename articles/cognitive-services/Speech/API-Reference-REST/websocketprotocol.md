---
title: Microsoft 语音识别 WebSocket 协议 | Microsoft Docs
description: 语音服务 WebSockets 协议文档
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 17954536e8bdb49c09204c2e522586b79cb1bef5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365706"
---
# <a name="speech-service-websocket-protocol"></a>语音服务 WebSocket 协议

  语音服务是基于云的平台，采用可用于将语音转换为文本的先进算法。 语音服务协议定义客户端应用程序与服务之间的[连接设置](#connection-establishment)以及对应方之间交换的语音识别消息（[客户端发起的消息](#client-originated-messages)和[服务发起的消息](#service-originated-messages)）。 此外，还介绍了[遥测消息](#telemetry-schema)和[错误处理](#error-handling)。

## <a name="connection-establishment"></a>建立连接

语音服务协议遵循 WebSocket 标准规范 [IETF RFC 6455 ](https://tools.ietf.org/html/rfc6455)。 WebSocket 连接从 HTTP 请求开始，该请求包含 HTTP 标头，此标头指示客户端希望将连接升级到 WebSocket 而不是使用 HTTP 语义。 服务器通过返回 HTTP `101 Switching Protocols` 响应来指示其参与 WebSocket 连接的意愿。 交换此握手后，客户端和服务都保持套接字打开，并开始使用基于消息的协议来发送和接收信息。

若要开始 WebSocket 握手，客户端应用程序会向服务发送 HTTPS GET 请求。 其中包括标准 WebSocket 升级标头以及特定于语音的其他标头。

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

服务响应：

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

所有语音请求都需要 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) 加密。 不支持使用未加密的语音请求。 支持以下 TLS 版本：

* TLS 1.2

### <a name="connection-identifier"></a>连接标识符

语音服务要求所有客户端都包含唯一 ID，用于标识连接。 客户端在启动 WebSocket 握手时必须包含 X-ConnectionId 标头。 X-ConnectionId 标头必须是[通用唯一标识符](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID) 值。 WebSocket 升级请求出现这些情况时，将被服务拒绝并返回 HTTP `400 Bad Request` 响应：未包含 X-ConnectionId、未给 X-ConnectionId 标头指定值，或未包含有效的 UUID 值。

### <a name="authorization"></a>授权

除了标准 WebSocket 握手标头之外，语音请求还需要 Authorization 标头。 无此标头的连接请求将被服务拒绝并返回 HTTP `403 Forbidden` 响应。

Authorization 标头必须包含 JSON Web 令牌 (JWT) 访问令牌。

若要详细了解如何订阅和获取用于检索有效 JWT 访问令牌的 API 密钥，请参阅[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页。

API 密钥将传递给令牌服务。 例如：

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

令牌访问需要以下标头信息。

| 名称 | 格式 | 说明 |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | 订阅密钥 |

令牌服务将 JWT 访问令牌返回为 `text/plain`。 然后将 JWT 作为 `Base64 access_token` 传递给握手，作为以 `Bearer` 为前缀字符串的 Authorization 标头。 例如：

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookie

客户端必须支持 [RFC 6265](https://tools.ietf.org/html/rfc6265) 中指定的 HTTP Cookie。

### <a name="http-redirection"></a>HTTP 重定向

客户端必须支持 [HTTP 协议规范](http://www.w3.org/Protocols/rfc2616/rfc2616.html)指定的标准重定向机制。

### <a name="speech-endpoints"></a>语音终结点

客户端必须使用语音服务的适当终结点。 终结点基于识别模式和语言。 下表展示了一些示例。

| Mode | 路径 | 服务 URI |
| -----|-----|-----|
| 交互 | /speech/recognition/interactive/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| 对话 | /speech/recognition/conversation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| 听写 | /speech/recognition/dictation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

有关详细信息，请参阅[服务 URI](../GetStarted/GetStartedREST.md#service-uri) 页。

### <a name="report-connection-problems"></a>报告连接问题

客户端应立即报告连接时遇到的所有问题。 有关报告连接失败的消息协议在[连接失败遥测](#connection-failure-telemetry)中进行了描述。

### <a name="connection-duration-limitations"></a>连接持续时间限制

与典型的 Web 服务 HTTP 连接相比，WebSocket 连接可持续很长时间。 语音服务为 WebSocket 与服务的连接持续时间设置了限制：

 * 任何活动 WebSocket 连接的最长持续时间为 10 分钟。 如果服务或客户端通过该连接发送 WebSocket 消息，则连接处于活动状态。 达到时间限制时，服务会在不发出警告的情况下终止连接。 客户端应开发不需要连接在达到或接近最长连接生存期时保持活动状态的用户方案。

 * 任何非活动 WebSocket 连接的最长持续时间为 180 秒。 如果服务和客户端都没有通过连接发送 WebSocket 消息，则连接处于非活动状态。 达到最长非活动生存期后，服务将终止非活动 WebSocket 连接。

## <a name="message-types"></a>消息类型

在客户端和服务之间建立 WebSocket 连接后，客户端和服务都可以发送消息。 本节介绍这些 WebSocket 消息的格式。

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) 指定 WebSocket 消息可使用文本或二进制编码来传输数据。 这两种编码使用不同的在线格式。 每种格式都经过优化，可有效地对消息有效负载进行编码、传输和解码。

### <a name="text-websocket-messages"></a>文本 WebSocket 消息

文本 WebSocket 消息携带文本信息的有效负载，该文本信息包含标头的一部分和由使用常用于 HTTP 消息的双回车换行符对分隔的正文。 并且，与 HTTP 消息一样，文本 WebSocket 消息以“名称: 值”格式指定标头，由单回车换行符对分隔。 文本 WebSocket 消息中包含的任何文本都必须使用 [UTF-8](https://tools.ietf.org/html/rfc3629) 编码。

文本 WebSocket 消息必须在标头 Path 中指定消息路径。 此标头的值必须是本文档后面定义的语音协议消息类型之一。

### <a name="binary-websocket-messages"></a>二进制 WebSocket 消息

二进制 WebSocket 消息携带二进制有效负载。 在语音服务协议中，通过使用二进制 WebSocket 消息将音频发送到服务和从服务接收音频。 所有其他消息都是文本 WebSocket 消息。 

与文本 WebSocket 消息一样，二进制 WebSocket 消息由标头和正文部分组成。 二进制 WebSocket 消息的前 2 个字节以 [big-endian](https://en.wikipedia.org/wiki/Endianness) 顺序指定标头部分的 16 位整数大小。 最小标头部分大小为 0 字节。 最大大小为 8,192 字节。 二进制 WebSocket 消息标头中的文本必须使用 [US-ASCII](https://tools.ietf.org/html/rfc20) 编码。

二进制 WebSocket 消息中的标头采用与文本 WebSocket 消息标头相同的编码格式。 “名称: 值”格式以单回车换行符对分隔。 二进制 WebSocket 消息必须在标头 Path 中指定消息路径。 此标头的值必须是本文档后面定义的语音协议消息类型之一。

语音服务协议中使用了文本和二进制 WebSocket 消息。 

## <a name="client-originated-messages"></a>客户端发起的消息

建立连接后，客户端和服务都可以开始发送消息。 本节介绍客户端应用程序发送到语音服务的消息的格式和有效负载。 [服务发起的消息](#service-originated-messages)一节中介绍了由语音服务发起并发送到客户端应用程序的消息。

客户端发送给服务的主要消息包括 `speech.config`、`audio` 和 `telemetry` 消息。 在详细介绍每种消息之前，先介绍所有这些消息的常见必需消息标头。

### <a name="required-message-headers"></a>必需的消息标头

所有客户端发起的消息都需要以下标头。

| 标头 | 值 |
|----|----|
| 路径 | 本文档中指定的消息路径 |
| X-RequestId | 采用“无连字符”格式的 UUID |
| X-Timestamp | 采用 ISO 8601 格式的客户端 UTC 时钟时间戳 |

#### <a name="x-requestid-header"></a>X-RequestId 标头

客户端发起的请求由 X-RequestId 消息标头进行唯一标识。 所有客户端发起的消息都需要此标头。 X-RequestId 标头值必须是“无连字符”形式的 UUID，例如 123e4567e89b12d3a456426655440000。 而不能采用规范形式 123e4567-e89b-12d3-a456-426655440000。 无 X-RequestId 标头或使用错误 UUID 格式的标头值的请求会导致服务终止 WebSocket 连接。

#### <a name="x-timestamp-header"></a>X-Timestamp 标头

客户端应用程序发送到语音服务的每条消息都必须包含 X-Timestamp 标头。 此标头的值是客户端发送该消息的时间。 无 X-Timestamp 标头或使用错误格式的标头值的请求会导致服务终止 WebSocket 连接。

X-Timestamp 标头值的格式必须为 'yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'ffffffff'。其中 'fffffff' 表示秒数的小数位。 例如，“12.5”表示“12 + 5/10 秒”，“12.526”表示“12 + 526/1000 秒”。 此格式符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)，与标准 HTTP Date 标头不同，它可以提供毫秒级的分辨率。 客户端应用程序可能会将时间戳四舍五入到最接近的毫秒数。 客户端应用程序需要使用[网络时间协议 (NTP) 服务器](https://en.wikipedia.org/wiki/Network_Time_Protocol)确保设备时钟准确跟踪时间。

### <a name="message-speechconfig"></a>消息 `speech.config`

语音服务需要了解应用程序的特征，以提供最佳的语音识别。 所需的特征数据包括有关为应用程序提供支持的设备和操作系统信息。 在 `speech.config` 消息中提供此信息。

客户端在建立与语音服务的连接之后以及发送任何 `audio` 消息之前，必须立即发送 `speech.config` 消息。 每个连接只需发送一次 `speech.config` 消息。

| 字段 | 说明 |
|----|----|
| WebSocket 消息编码 | 文本 |
| Body | 有效负载作为 JSON 结构 |

#### <a name="required-message-headers"></a>必需的消息标头

| 标头名称 | 值 |
|----|----|
| 路径 | `speech.config` |
| X-Timestamp | 采用 ISO 8601 格式的客户端 UTC 时钟时间戳 |
| Content-Type | application/json; charset=utf-8 |

与语音服务协议中所有客户端发起的消息一样，`speech.config` 消息必须包含 X-Timestamp 标头，该标头记录消息发送到服务时的客户端 UTC 时钟时间。 `speech.config` 消息不需要 X-RequestId 标头，因为此消息与特定语音请求无关。

#### <a name="message-payload"></a>消息有效负载
`speech.config` 消息的有效负载采用 JSON 结构，其中包含有关应用程序的信息。 以下示例显示了此信息。 客户端和设备上下文信息包含在 JSON 结构的 context 元素中。 

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>系统元素

`speech.config` 消息的 system.version 元素包含客户端应用程序或设备使用的语音 SDK 软件的版本信息。 该值采用 major.minor.build.branch 格式。 如果分支组件不适用，可将其省略。

##### <a name="os-element"></a>操作系统元素

| 字段 | 说明 | 使用情况 |
|-|-|-|
| os.platform | 承载应用程序的操作系统平台，例如 Windows、Android、iOS 或 Linux |必选 |
| os.name | 操作系统产品名称，例如 Debian 或 Windows 10 | 必选 |
| os.version | 操作系统的版本，格式为 major.minor.build.branch | 必选 |

##### <a name="device-element"></a>设备元素

| 字段 | 说明 | 使用情况 |
|-|-|-|
| device.manufacturer | 设备硬件制造商 | 必选 |
| device.model | 设备型号 | 必选 |
| device.version | 设备制造商提供的设备软件版本。 此值指定制造商可跟踪的设备版本。 | 必选 |

### <a name="message-audio"></a>消息 `audio`

启用语音的客户端应用程序通过将音频流转换为一系列音频区块来将音频发送到语音服务。 每个音频区块携带一段要由服务进行听录的语音。 单个音频区块的最大大小为 8,192 字节。 音频流消息是二进制 WebSocket 消息。

客户端使用 `audio` 消息将音频区块发送到服务。 客户端以区块的形式从麦克风中读取音频，并将这些区块发送到语音服务进行听录。 第一个 `audio` 消息必须包含格式标准的标头，该标头正确指定音频符合服务支持的编码格式之一。 其他 `audio` 消息仅包含从麦克风读取的二进制音频流数据。

客户端可选择发送带有零长度正文的 `audio` 消息。 此消息向服务告知：客户端知道用户停止说话、语句结束和麦克风关闭。

语音服务使用包含唯一请求标识符的第一条 `audio` 消息来表示新请求/响应周期或轮次的开始。 当服务收到带有新请求标识符的 `audio` 消息后，它会放弃与任何上一轮相关的任何排队中或未发送的消息。

| 字段 | 说明 |
|-------------|----------------|
| WebSocket 消息编码 | 二进制 |
| Body | 音频区块的二进制数据。 最大大小为 8,192 字节。 |

#### <a name="required-message-headers"></a>必需的消息标头

所有 `audio` 消息都需要以下标头。

| 标头         |  值     |
| ------------- | ---------------- |
| 路径 | `audio` |
| X-RequestId | 采用“无连字符”格式的 UUID |
| X-Timestamp | 采用 ISO 8601 格式的客户端 UTC 时钟时间戳 |
| Content-Type | 音频内容类型。 类型必须是 audio/x-wav (PCM) 或 audio/silk (SILK)。 |

#### <a name="supported-audio-encodings"></a>支持的音频编码

本节介绍语音服务支持的音频编解码器。

##### <a name="pcm"></a>PCM

语音服务接受未压缩的脉冲编码调制 (PCM) 音频。 音频以 [WAV](https://en.wikipedia.org/wiki/WAV) 格式发送到服务，因此第一个音频区块必须包含有效的[资源交换文件格式](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF) 标头。 如果客户端使用不包含有效 RIFF 标头的音频区块启动轮次，则服务会拒绝该请求并终止 WebSocket 连接。

PCM 音频必须以 16 kHz 采样，每个样本 16 比特，单通道 (riff-16khz-16bit-mono-pcm)。 语音服务不支持立体声音频流，并拒绝未使用指定比特率、采样率或通道数的音频流。

##### <a name="opus"></a>Opus

Opus 是一款开放、免版税、功能多样的音频编解码器。 语音服务支持采用 `32000` 或 `16000` 恒定比特率的 Opus。 目前仅支持 `audio/ogg` MIME 类型指定的 Opus `OGG` 容器。

若要使用 Opus，请修改 [JavaScript 示例](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101)并更改 `RecognizerSetup` 方法以返回。

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>检测语音结束

当人们结束讲话后，并不会给出明确的结束信号。 任何接受语音作为输入的应用程序都有两种用来处理音频流中语音结束的选择：服务语音结束检测和客户端语音检测。 在这两种选择中，服务语音结束检测通常能够提供更好的用户体验。

##### <a name="service-end-of-speech-detection"></a>服务语音结束检测

若要构建理想的免提语音体验，应用程序需允许服务检测用户何时结束说话。 客户端将来自麦克风的音频作为音频区块发送，直到服务检测到静音并用 `speech.endDetected` 消息返回响应。

##### <a name="client-end-of-speech-detection"></a>客户端语音结束检测

允许用户以某种方式发出语音结束信号的客户端应用程序也可以向服务提供该信号。 例如，客户端应用程序可能设有用户可按下的“停止”或“静音”按钮。 若要发出语音结束信号，客户端应用程序会发送带有零长度正文的音频区块消息。 语音服务将此消息解释为传入音频流的结束。

### <a name="message-telemetry"></a>消息 `telemetry`

客户端应用程序必须通过发送有关发至语音服务的轮次的遥测来确认每个轮次的结束。 轮次结束确认可让语音服务确保客户端正确接收完成请求及其响应所需的全部消息。 轮次结束确认还使语音服务能够验证客户端应用程序是否按预期执行。 如果在对启用语音的应用程序进行故障排除时需要帮助，此信息会非常有用。

客户端必须在收到 `turn.end` 消息后立即发送 `telemetry` 消息，确认轮次结束。 客户端应尽快尝试确认 `turn.end`。 如果客户端应用程序未能确认轮次结束，则语音服务可能会因错误而终止连接。 客户端必须为 X-RequestId 值标识的每个请求和响应仅发送一条 `telemetry` 消息。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `telemetry` |
| X-Timestamp | 采用 ISO 8601 格式的客户端 UTC 时钟时间戳 |
| Content-Type | `application/json` |
| Body | 包含有关轮次的客户端信息的 JSON 结构 |

[遥测架构](#telemetry-schema)一节中定义了 `telemetry` 消息正文的架构。

#### <a name="telemetry-for-interrupted-connections"></a>已中断连接的遥测

如果网络连接在轮次期间因任何原因失败并且客户端未从服务收到 `turn.end` 消息，则客户端会发送 `telemetry` 消息。 此消息描述客户端下次与服务建立连接时的失败请求。 客户端不必立即尝试连接以发送 `telemetry` 消息。 消息可能在客户端上缓冲，并通过将来用户请求的连接发送。 失败请求的 `telemetry` 消息必须使用来自失败请求的 X-RequestId 值。 建立连接后，它就可能被发送到服务，无需等待发送或接收其他消息。

## <a name="service-originated-messages"></a>服务发起的消息

本节介绍由语音服务发起并发送到客户端的消息。 语音服务维护客户端功能的注册表并生成每个客户端所需的消息，因此并非所有客户端都会收到此处描述的所有消息。 为简洁起见，消息由 Path 标头的值引用。 例如，我们将带有 Path 值 `speech.hypothesis` 的 WebSocket 文本消息称为 speech.hypothesis 消息。

### <a name="message-speechstartdetected"></a>消息 `speech.startDetected`

`speech.startDetected` 消息指示语音服务已检测到音频流中的语音。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Body | JSON 结构，包含检测到语音开始时的条件信息。 此结构中的 Offset 字段指定在音频流中检测到语音时的位置相对于流开始位置的偏移（以 100 纳秒为单位）。 |

#### <a name="sample-message"></a>示例消息

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>消息 `speech.hypothesis`

在语音识别期间，语音服务会定期生成有关服务已识别的单词的假设。 语音服务按约每 300 毫秒一次的频率将这些假设发送给客户端。 `speech.hypothesis` 仅适用于增强用户语音体验。 不能依赖这些消息中文本的内容或准确性。

 `speech.hypothesis` 消息适用于符合这些描述的客户端：具有一定的文本呈现功能，并希望向讲话者提供正在进行的识别的近实时反馈。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `speech.hypothesis` |
| X-RequestId | 采用“无连字符”格式的 UUID |
| Content-Type | application/json |
| Body | 语音假设 JSON 结构 |

#### <a name="sample-message"></a>示例消息

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

Offset 元素指定识别出短语时的位置相对于音频流开头位置的偏移（以 100 纳秒为单位）。

Duration 元素指定该语音短语的持续时间（以 100 纳秒为单位）。

客户端不得对语音假设中包含的频率、时间或文本或任何两个语音假设中的文本一致性做出任何假设。 这些假设只是服务中听录过程的快照。 它们不代表稳定的听录累计。 例如，第一个语音假设可能包含“精彩乐趣”，第二个假设可能包含“发现乐趣”。 语音服务不会对语音假设中的文本执行任何后续处理（例如，大写、标点符号）。

### <a name="message-speechphrase"></a>消息 `speech.phrase`

当语音服务确定信息已足以产生不会更改的识别结果时，该服务会生成 `speech.phrase` 消息。 语音服务会在检测到用户完成句子或短语后生成这些结果。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `speech.phrase` |
| Content-Type | application/json |
| Body | 语音短语 JSON 结构 |

语音短语 JSON 架构包括以下字段：`RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 有关这些字段的详细信息，请参阅[听录响应](../concepts.md#transcription-responses)。

#### <a name="sample-message"></a>示例消息

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>消息 `speech.endDetected`

`speech.endDetected` 消息指定客户端应用程序应停止向服务流式传输音频。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `speech.endDetected` |
| Body | 包含检测到语音结束时的偏移的 JSON 结构。 偏移从用于识别的音频开始位置计算，以 100 纳秒为单位表示。 |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>示例消息

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Offset 元素指定识别出短语时的位置相对于音频流开头位置的偏移（以 100 纳秒为单位）。

### <a name="message-turnstart"></a>消息 `turn.start`

`turn.start` 从服务透视发出轮次开始的信号。 `turn.start` 消息始终是任何请求收到的第一条响应消息。 如果未收到 `turn.start` 消息，则假定服务连接的状态无效。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| Body | JSON 结构 |

#### <a name="sample-message"></a>示例消息

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

`turn.start` 消息的正文是一个 JSON 结构，其中包含轮次开始的上下文。 context 元素包含 serviceTag 属性。 此属性指定服务与轮次关联的标记值。 如需帮助排除应用程序中的故障，Microsoft 可使用此值。

### <a name="message-turnend"></a>消息 `turn.end`

`turn.end` 从服务透视发出轮次结束的信号。 `turn.end` 消息始终是任何请求收到的最后一条响应消息。 客户端可使用此消息的接收作为清除活动和转换到空闲状态的信号。 如果未收到 `turn.end` 消息，则假定服务连接的状态无效。 在这些情况下，请关闭与服务的现有连接并重新连接。

| 字段 | 说明 |
| ------------- | ---------------- |
| WebSocket 消息编码 | 文本 |
| 路径 | `turn.end` |
| Body | 无 |

#### <a name="sample-message"></a>示例消息

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>遥测架构

telemetry 消息的正文是 JSON 结构，其中包含有关轮次或尝试连接的客户端信息。 该结构由记录客户端事件发生时间的客户端时间戳组成。 每个时间戳必须采用 ISO 8601 格式，如标题为“X-Timestamp 标头”的小节中所述。 未指定 JSON 结构中的所有必填字段或未使用正确时间戳格式的 telemetry 消息，可能会导致服务终止与客户端的连接。 客户端必须为所有必填字段提供有效值。 客户端应在适当的时候为可选字段提供值。 本节示例中显示的值仅供参考。

遥测架构分为以下部分：收到的消息时间戳和指标。 以下各节详细说明了每部分的格式和用法。

### <a name="received-message-time-stamps"></a>收到的消息时间戳

客户端必须包含成功连接到服务后收到的所有消息的接收时间值。 这些值必须记录客户端从网络接收到每条消息的时间。 并且不应记录任何其他时间。 例如，客户端不应记录它对消息执行操作的时间。 在“名称: 值”对的数组中指定收到的消息时间戳。 该对的名称指定消息的 Path 值。 该对的值指定收到消息时的客户端时间。 或者，如果收到多个指定名称的消息，则该对的值是一个指示何时收到这些消息的时间戳数组。

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

客户端必须通过在 JSON 正文中包含这些消息的时间戳来确认已收到服务发出的所有消息。 如果客户端未能确认收到消息，则该服务可能会终止连接。

### <a name="metrics"></a>度量值

客户端必须包含有关请求生命周期内发生的事件信息。 支持以下指标：`Connection`、`Microphone` 和 `ListeningTrigger`。

### <a name="metric-connection"></a>指标 `Connection`

`Connection` 指标指定客户端连接尝试的详细信息。 该指标必须包括 WebSocket 连接开始和完成时的时间戳。 仅在第一次连接时需要 `Connection` 指标。 后续轮次不需要包含此信息。 如果客户端在建立连接之前进行多次连接尝试，则应包括有关所有连接尝试的信息。 有关详细信息，请参阅[连接失败遥测](#connection-failure-telemetry)。

| 字段 | 说明 | 使用情况 |
| ----- | ----------- | ----- |
| 名称 | `Connection` | 必选 |
| ID | 此连接请求的 X-ConnectionId 标头中使用的连接标识符值 | 必选 |
| 开始 | 客户端发出连接请求的时间 | 必选 |
| 结束 | 客户端收到成功建立连接的通知时间，或出错时收到拒绝、弃用或失败的通知时间 | 必选 |
| 错误 | 已发生错误的描述（如有）。 如果连接成功，客户端应省略此字段。 此字段的最大长度为 50 个字符。 | 出错时必填，否则省略 |

错误描述最多可填 50 个字符，理想情况下应填入下表中列出的值之一。 如果下表中没有能够匹配错误条件的值，则客户端可使用无空格的 [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) 格式填写错误条件的简短描述。 发送 telemetry 消息需要连接到服务，因此只能在 telemetry 消息中报告暂时或临时错误条件。 永久阻止客户端建立与服务的连接的错误条件会阻止客户端向服务发送任何消息，包括 telemetry 消息。

| 错误 | 使用情况 |
| ----- | ----- |
| DNSfailure | 由于网络堆栈中的 DNS 故障，客户端无法连接到该服务。 |
| NoNetwork | 客户端尝试连接，但网络堆栈报告无可用的物理网络。 |
| NoAuthorization | 尝试获取连接的授权令牌时，客户端连接失败。 |
| NoResources | 尝试建立连接时，客户端耗尽了一些本地资源（例如内存）。 |
| 禁止 | 客户端无法连接到该服务，因为该服务在 WebSocket 升级请求上返回了 HTTP `403 Forbidden` 状态代码。 |
| 未授权 | 客户端无法连接到该服务，因为该服务在 WebSocket 升级请求上返回了 HTTP `401 Unauthorized` 状态代码。 |
| BadRequest | 客户端无法连接到该服务，因为该服务在 WebSocket 升级请求上返回了 HTTP `400 Bad Request` 状态代码。 |
| ServerUnavailable | 客户端无法连接到该服务，因为该服务在 WebSocket 升级请求上返回了 HTTP `503 Server Unavailable` 状态代码。 |
| ServerError | 客户端无法连接到该服务，因为该服务在 WebSocket 升级请求上返回了 `HTTP 500` 内部错误状态代码。 |
| 超时 | 服务无响应，客户端连接请求超时。 “结束”字段包含客户端超时和停止等待连接时的时间。 |
| ClientError | 由于某些内部客户端错误，客户端已终止连接。 | 

### <a name="metric-microphone"></a>指标 `Microphone`

所有语音轮次都需要 `Microphone` 指标。 该指标在音频输入被主动用于语音请求期间在客户端上测量时间。

使用以下示例作为在客户端应用程序中记录 `Microphone` 指标的开始时间值的准则：

* 客户端应用程序要求用户必须通过按下物理按钮来启动麦克风。 按下按钮后，客户端应用程序从麦克风读取输入并将其发送到语音服务。 当麦克风完成初始化并准备好提供输入时，`Microphone` 指标的开始值记录按钮按下时的时间。 `Microphone` 指标的结束值记录客户端应用程序在从服务收到 `speech.endDetected` 消息后停止向服务流式传输音频时的时间。

* 客户端应用程序使用“始终”侦听的关键字监视器。 只有在关键字监视器检测到语音触发短语后，客户端应用程序才会收集来自麦克风的输入并将其发送到语音服务。 `Microphone` 指标的开始值记录关键字监视器通知客户端开始使用来自麦克风的输入时的时间。 `Microphone` 指标的结束值记录客户端应用程序在从服务收到 `speech.endDetected` 消息后停止向服务流式传输音频时的时间。

* 客户端应用程序可访问恒定音频流，并在语音检测模块中对该音频流执行静音/语音检测。 `Microphone` 指标的开始值记录语音检测模块通知客户端开始使用来自音频流的输入时的时间。 `Microphone` 指标的结束值记录客户端应用程序在从服务收到 `speech.endDetected` 消息后停止向服务流式传输音频时的时间。

* 客户端应用程序正在处理多轮次请求的第二个轮次，并收到服务响应消息通知打开麦克风以收集第二轮输入。 `Microphone` 指标的开始值记录客户端应用程序启用麦克风并开始使用来自该音频源的输入时的时间。 `Microphone` 指标的结束值记录客户端应用程序在从服务收到 `speech.endDetected` 消息后停止向服务流式传输音频时的时间。

`Microphone` 指标的结束时间值记录客户端应用程序停止流式传输音频时的时间。 在大多数情况下，此事件在客户端从服务收到 `speech.endDetected` 消息后随即发生。 客户端应用程序可通过确保 `Microphone` 指标的结束时间值晚于 `speech.endDetected` 消息的接收时间值来验证它们是否正确符合协议。 并且，由于在一个轮次的结束和另一个轮次的开始之间往往存在延迟，因此客户端也可通过以下方法来验证是否符合协议：确保任何后续轮次 `Microphone` 指标的开始时间正确记录了客户端开始使用麦克风将音频输入流式传输到服务时的时间。

| 字段 | 说明 | 使用情况 |
| ----- | ----------- | ----- |
| 名称 | 麦克风 | 必选 |
| 开始 | 客户端开始使用来自麦克风或其他音频流的音频输入或从关键字监视器收到触发时的时间 | 必选 |
| 结束 | 客户端停止使用麦克风或音频流时的时间 | 必选 |
| 错误 | 已发生错误的描述（如有）。 如果麦克风操作成功，客户端应省略此字段。 此字段的最大长度为 50 个字符。 | 出错时必填，否则省略 |

### <a name="metric-listeningtrigger"></a>指标 `ListeningTrigger`
`ListeningTrigger` 指标测量用户执行启动语音输入的操作时的时间。 `ListeningTrigger` 指标是可选的，但建议可提供此指标的客户端执行此操作。

使用以下示例作为在客户端应用程序中记录  *指标的开始和结束时间值的准则*`ListeningTrigger`。

* 客户端应用程序要求用户必须通过按下物理按钮来启动麦克风。 此指标的开始值记录按钮按下时的时间。 结束值记录按钮按下完成时的时间。

* 客户端应用程序使用“始终”侦听的关键字监视器。 在关键字监视器检测到语音触发短语后，客户端应用程序会读取来自麦克风的输入并将其发送到语音服务。 该指标的开始值记录关键字监视器接收到即将被检测为触发短语的音频时的时间。 结束值记录用户说出触发短语的最后一个字时的时间。

* 客户端应用程序可访问恒定音频流，并在语音检测模块中对该音频流执行静音/语音检测。 该指标的开始值记录语音检测模块接收到即将被检测为语音的音频时的时间。 结束值记录语音检测模块检测到语音时的时间。

* 客户端应用程序正在处理多轮次请求的第二个轮次，并收到服务响应消息通知打开麦克风以收集第二轮输入。 客户端应用程序本轮次不应包含 `ListeningTrigger` 指标。

| 字段 | 说明 | 使用情况 |
| ----- | ----------- | ----- |
| 名称 | ListeningTrigger | 可选 |
| 开始 | 客户端侦听触发开始时的时间 | 必选 |
| 结束 | 客户端侦听触发完成时的时间 | 必选 |
| 错误 | 已发生错误的描述（如有）。 如果触发操作成功，客户端应省略此字段。 此字段的最大长度为 50 个字符。 | 出错时必填，否则省略 |

#### <a name="sample-message"></a>示例消息

以下示例显示了同时包含 ReceivedMessages 和 Metrics 部分的遥测消息：

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>错误处理。

本节介绍应用程序需要处理的错误消息和错误条件的类型。

### <a name="http-status-codes"></a>HTTP 状态代码

在 WebSocket 升级请求期间，语音服务可能会返回任何标准 HTTP 状态代码，例如 `400 Bad Request` 等。应用程序必须正确处理这些错误条件。

#### <a name="authorization-errors"></a>授权错误

如果在 WebSocket 升级期间提供了不正确的授权，则语音服务将返回 HTTP `403 Forbidden` 状态代码。 其中可触发此错误代码的条件包括：

* 缺少 Authorization 标头

* 授权令牌无效

* 授权令牌过期

`403 Forbidden` 错误消息并不指示语音服务有问题。 此错误消息指示客户端应用程序出现问题。

### <a name="protocol-violation-errors"></a>违反协议错误

如果语音服务检测到来自客户端的任何违反协议行为，则服务会在返回状态代码和终止原因后终止 WebSocket 连接。 客户端应用程序可使用此信息来排除故障并修复违规行为。

#### <a name="incorrect-message-format"></a>消息格式不正确

如果客户端向未按本规范中给出的正确格式编码的服务发送文本或二进制消息，则服务将返回“1007 有效负载数据无效”状态代码并关闭连接。 

该服务返回此状态代码的原因有多种，如以下示例所示：

* “消息格式不正确。 二进制消息的标头大小前缀无效。” 客户端发送了带有无效标头大小前缀的二进制消息。

* “消息格式不正确。 二进制消息的标头大小无效。” 客户端发送了指定了无效标头大小的二进制消息。

* “消息格式不正确。 将二进制消息标头解码为 UTF-8 时失败。” 客户端发送的二进制消息中包含未采用 UTF-8 正确编码的标头。

* “消息格式不正确。 文本消息未包含任何数据。” 客户端发送的文本消息中未包含正文数据。

* “消息格式不正确。 将文本消息解码为 UTF-8 失败。” 客户端发送的文本消息未采用 UTF-8 正确编码。

* “消息格式不正确。 文本消息未包含标头分隔符。” 客户端发送的文本消息中未包含标头分隔符或使用了错误的标头分隔符。

#### <a name="missing-or-empty-headers"></a>标头缺失或为空

如果客户端发出的消息没有所需标头 X-RequestId 或 Path，则服务将返回“1002 协议错误”状态代码并关闭连接。 消息内容为“标头缺失或为空。 {标头名称}。”

#### <a name="requestid-values"></a>RequestId 值

如果客户端发出的消息中指定了格式不正确的 X-RequestId 标头，则服务将关闭连接并返回“1002 协议错误”状态。 消息内容为“请求无效。 未以无连字符 UUID 格式指定 X-RequestId 标头值。”

#### <a name="audio-encoding-errors"></a>音频编码错误

如果客户端发送用于启动轮次的的音频区块并且音频格式或编码不符合所需规范，则服务将关闭连接并返回“1007 有效负载数据无效”状态代码。 该消息指示格式编码错误源。

#### <a name="requestid-reuse"></a>RequestId 重用

轮次完成后，如果客户端发送一条用于从该轮次开始重用请求标识符的消息，则服务将关闭连接并返回“1002 协议错误”状态代码。 消息内容为“请求无效。 不允许重用请求标识符。”

## <a name="connection-failure-telemetry"></a>连接失败遥测

为确保最佳用户体验，客户端必须使用 telemetry 消息向语音服务通知连接中重要检查点的时间戳。 客户端通知服务已尝试但失败的连接，这同样重要。

对于每次失败的连接尝试，客户端都会创建一个具有唯一 X-RequestId 标头值的 telemetry 消息。 由于客户端无法建立连接，因此可省略 JSON 正文中的 ReceivedMessages 字段。 仅包含指标字段中的 `Connection` 条目。 该条目包括开始和结束时间戳以及遇到的错误条件。

### <a name="connection-retries-in-telemetry"></a>遥测中的连接重试

客户端应通过触发连接尝试的事件来区分重试和多次连接尝试。 重试是在没有任何用户输入的情况下以编程方式执行的连接尝试。 多次连接尝试是为响应用户输入而执行的多次连接尝试。 客户端为每个用户触发的连接尝试提供唯一的 X-RequestId 和 telemetry 消息。 客户端重用 X-RequestId 以编程方式进行重试。 如果对单个连接尝试进行了多次重试，则每次重试尝试都会作为 `Connection` 条目包含在 telemetry 消息中。

例如，假设用户说出关键字来触发启动连接，并且由于 DNS 错误导致第一次连接尝试失败。 但是，客户端以编程方式进行的第二次尝试成功。 由于客户端在不需要用户额外输入的情况下重试连接，因此客户端使用具有多个 `Connection` 条目的单个 telemetry 消息来描述连接。

又例如，假设用户说出关键字来触发启动连接，并且在三次重试后连接尝试失败。 然后客户端放弃，停止尝试连接到服务，并通知用户出现错误。 随后，用户再次说出关键字进行触发。 这一次，假设客户端连接到服务。 连接后，客户端立即向服务发送 telemetry 消息，其中包含三个描述连接失败的 `Connection` 条目。 客户端收到 `turn.end` 消息后，会发送另一条描述成功连接的 telemetry 消息。

## <a name="error-message-reference"></a>错误消息参考

### <a name="http-status-codes"></a>HTTP 状态代码

| HTTP 状态代码 | 说明 | 故障排除 |
| - | - | - |
| 400 错误请求 | 客户端发送的 WebSocket 连接请求不正确。 | 请检查是否提供了所有必需的参数和 HTTP 标头，值是否正确。 |
| 401 未授权 | 客户端未包含所需的授权信息。 | 请检查是否在 WebSocket 连接中发送了 Authorization 标头。 |
| 403 禁止访问 | 客户端发送了授权信息，但该信息无效。 | 请检查是否在 Authorization 标头中发送了过期或无效的值。 |
| 404 未找到 | 客户端尝试访问了不受支持的 URL 路径。 | 请检查是否使用了正确的 WebSocket 连接 URL。 |
| 500 服务器错误 | 该服务遇到内部错误，无法满足请求。 | 大多数情况下，此错误是暂时的。 重试请求。 |
| 503 服务不可用 | 服务无法处理该请求。 | 大多数情况下，此错误是暂时的。 重试请求。 |

### <a name="websocket-error-codes"></a>WebSocket 错误代码

| WebSocketsStatus 代码 | 说明 | 故障排除 |
| - | - | - |
| 1000 正常关闭 | 该服务关闭了 WebSocket 连接并且没有发生错误。 | 如果 WebSocket 意外关闭，请重新阅读文档，确保已了解服务终止 WebSocket 连接的方式和时间点。 |
| 1002 协议错误 | 客户端未遵守协议要求。 | 请确保已了解协议文档并明确要求。 阅读之前有关错误原因的文档，查看是否违反了协议要求。 |
| 1007 有效负载无效 | 客户端在协议消息中发送了无效的有效负载。 | 请检查发送到服务的最后一条消息是否存在错误。 阅读之前有关有效负载错误的文档。 |
| 1011 服务器错误 | 该服务遇到内部错误，无法满足请求。 | 大多数情况下，此错误是暂时的。 重试请求。 |

## <a name="related-topics"></a>相关主题

请参阅基于 WebSocket 的语音服务协议的 [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) 实现。
