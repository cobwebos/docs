---
title: Microsoft 语音翻译 API 参考 | Microsoft Docs
titleSuffix: Cognitive Services
description: Microsoft 语音翻译 API 的参考文档。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: be8faddf56158de3399713c41638c0b913b4627e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366814"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft 语音翻译 API

此服务提供一个流式处理 API，用于将对话语音从一种语言转录为另一种语言的文本。 此 API 还集成了文本转语音功能，可以将翻译的文本再次转换成语音。 Microsoft 语音翻译 API 可以实现实时翻译对话这样的场景，就像在 Skype 翻译中看到的那样。

借助 Microsoft 语音翻译 API，客户端应用程序可将语音音频流式传输到服务和接收基于文本的结果，包括在源语言中识别的文本及其目标语言翻译。 通过将深度神经网络支持的自动语音识别 (ASR) 应用到音频流，来生成文本结果。 名为 TrueText 的新技术会进一步改善原始 ASR 输出，以更准确地反映用户意向。 例如，TrueText 会删除不流畅的语流（“嗯”和咳嗽声），并还原正确的标点和大小写。 此外，还包括屏蔽或排除亵渎字词的功能。 识别和翻译引擎经过专门的训练，可以处理对话语音。 语音翻译服务使用寂静检测技术来确定表述的末尾。 在语音活动停顿后，服务会流式传回已完成的表述的最终结果。 服务还能发回部分结果，对进行中的表述执行中间识别和翻译。 对于最终结果，服务可提供从目标语言中的讲述文本合成语音的功能（文本转语音）。 文本转语音音频以客户端指定的格式创建。 可以使用 WAV 和 MP3 格式。

Microsoft 语音翻译 API 利用 WebSocket 协议在客户端和服务器之间提供全双工信道。 应用程序会要求通过以下步骤来使用此服务：

## <a name="1-getting-started"></a>1.入门
若要访问 Microsoft 文本翻译 API，需[注册 Microsoft Azure](translator-speech-how-to-signup.md)。

## <a name="2-authentication"></a>2.身份验证

使用订阅密钥进行身份验证。 Microsoft 语音翻译 API 支持两种身份验证模式：

* **使用访问令牌：** 在应用程序中，从令牌服务获取访问令牌。 使用 Microsoft 语音翻译 API 订阅密钥，从认知服务身份验证服务获取访问令牌。 访问令牌的有效期为 10 分钟。 每 10 分钟获取一次新的访问令牌，在这 10 分钟内，始终对重复的请求使用同一访问令牌。

* **直接使用订阅密钥：** 在应用程序中，在 `Ocp-Apim-Subscription-Key` 标头中以值的形式传递订阅密钥。

将订阅密钥和访问令牌视为不应允许查看的机密。

## <a name="3-query-languages"></a>3.查询语言
**查询语言资源中是否存在最新的受支持语言组合。** [语言资源](languages-reference.md)公开一组适用于语音识别、文本翻译和文本转语音操作的语言和语音。 每种语言或语音都获得一个标识符，供 Microsoft 语音翻译 API 用来标识同一语言或语音。

## <a name="4-stream-audio"></a>4.对音频进行流式传输
**打开一个连接，开始将音频流式传输到服务。** 服务 URL 为 `wss://dev.microsofttranslator.com/speech/translate`。 服务预期的参数和音频格式介绍如下，位于 `/speech/translate` 操作中。 其中一个参数用于传递上面的步骤 2 中的访问令牌。

## <a name="5-process-the-results"></a>5.处理结果
**处理从服务流式传输回来的结果。** 部分结果、最终结果和文本转语音音频片段的格式在下面的 `/speech/translate` 操作的文档中介绍。

可以从 [Microsoft Translator Github 站点](https://github.com/MicrosoftTranslator)获得演示如何使用 Microsoft 语音翻译 API 的代码示例。

## <a name="implementation-notes"></a>实现说明

GET /speech/translate 建立用于语音翻译的会话

### <a name="connecting"></a>连接
连接到服务之前，请查看在此部分后面给出的参数列表。 示例请求为：

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

此请求指定将口语英语流式传输到服务，然后翻译为意大利语。 每个最终的识别结果将会使用名为 Elsa 的女性语音生成文本转语音音频响应。 请注意，请求将凭据包括在 `Ocp-Apim-Subscription-Key header` 中。 此请求还按照最佳做法在 `X-ClientTraceId` 标头中设置全局唯一标识符。 一个客户端应用程序会记录跟踪 ID，这样就可以在发生问题时使用该 ID 来排查问题。

### <a name="sending-audio"></a>发送音频
建立连接后，客户端开始将音频流式传输到服务。 客户端以区块的方式发送音频。 每个区块都使用类型为“二进制”的 Websocket 消息进行传输。

音频输入采用波形音频文件格式（WAVE，其更常用的名称为 WAV，来自其文件扩展名）。 客户端应用程序会流式传输单通道带符号 16 位 PCM 音频，采样率为 16 kHz。 由客户端流式传输的第一组字节将包括 WAV 标头。 采样率为 16 kHz 的单通道带符号 16 位 PCM 流的 44 字节标头为：

|Offset|值|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

请注意，总文件大小（4-7 字节）和 "data" 的大小（40-43 字节）设置为零。 对于总大小不一定提前知道的流式传输方案，这是可行的。

发送 WAV (RIFF) 标头以后，客户端发送音频数据区块。 客户端通常会流式传输固定大小的区块，这里的固定大小代表固定时长（例如，一次流式传输 100 毫秒的音频）。

### <a name="final-result"></a>最终结果
表述结束时会生成最终的语音识别结果。 结果使用类型为“文本”的 WebSocket 消息从服务传输到客户端。 消息内容是使用以下属性对对象进行的 JSON 序列化：

* `type`：用于标识结果类型的字符串常量。 最终结果的值为 final。
* `id`：分配给识别结果的字符串标识符。
* `recognition`：采用源语言的已识别文本。 如果是假的识别，文本可能为空字符串。
* `translation`：采用目标语言翻译的已识别文本。
* `audioTimeOffset`：开始识别时的时间偏移，以滴答为单位（1 滴答 = 100 纳秒）。 偏移是相对于流式传输开始的时候。
* `audioTimeSize`：识别的持续时间，以滴答（100 纳秒）为单位。
* `audioStreamPosition`：开始识别时的字节偏移。 偏移是相对于流开始的时候。
* `audioSizeBytes`：识别内容的大小，以字节为单位。

请注意，默认情况下，识别在音频流中的定位不包括在结果中。 客户端必须选择 `TimingInfo` 功能（参见 `features` 参数）。

最终结果示例如下：

```
{
  type: "final"
  id: "23",
  recognition: "what was said", 
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>部分结果
默认情况下，部分或中间语音识别结果不流式传输到客户端。 客户端可以使用功能查询参数来请求它们。

部分结果使用类型为“文本”的 WebSocket 消息从服务传输到客户端。 消息内容是使用以下属性对对象进行的 JSON 序列化：

* `type`：用于标识结果类型的字符串常量。 部分结果的值为 partial。
* `id`：分配给识别结果的字符串标识符。
* `recognition`：采用源语言的已识别文本。
* `translation`：采用目标语言翻译的已识别文本。
* `audioTimeOffset`：开始识别时的时间偏移，以滴答为单位（1 滴答 = 100 纳秒）。 偏移是相对于流式传输开始的时候。
* `audioTimeSize`：识别的持续时间，以滴答（100 纳秒）为单位。
* `audioStreamPosition`：开始识别时的字节偏移。 偏移是相对于流开始的时候。
* `audioSizeBytes`：识别内容的大小，以字节为单位。

请注意，默认情况下，识别在音频流中的定位不包括在结果中。 客户端必须选择 TimingInfo 功能（参见 features 参数）。

最终结果示例如下：

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was", 
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>文本转语音
启用文本转语音功能时（参见下面的 `features` 参数），最终结果后面还有语音翻译文本的音频。 语音数据会进行区块化，然后以一系列类型为“二进制”的 Websocket 消息的形式从服务发送到客户端。 客户端可以通过检查每条消息的 FIN 位来检测流的末尾。 最后一条二进制消息会将其 FIN 位设置为 1，表示流的末尾。 流的格式取决于 `format` 参数的值。

### <a name="closing-the-connection"></a>关闭连接
客户端应用程序在完成流式传输音频并收到最后一个最终结果以后，会通过启动 WebSocket 关闭握手来关闭连接。 存在会导致服务器终止连接的条件。 客户端可能会收到下面的 WebSocket 关闭代码：

* `1003 - Invalid Message Type`：服务器正在终止连接，因为无法接受所收到的数据类型。 当传入音频没有以正确的标头开头时，通常会发生这种情况。
* `1000 - Normal closure`：连接在履行请求后已关闭。 如果出现以下情况，服务器会关闭连接：长时间没有从客户端收到音频；长时间进行静默流式传输；会话达到允许的最大时长（大约 90 分钟）。
* `1001 - Endpoint Unavailable`：指示服务器会变得不可用。 客户端应用程序可能会尝试重新连接，但重试次数有限制。
* `1011 - Internal Server Error`：连接将由服务器关闭，因为服务器上出现错误。

### <a name="parameters"></a>parameters

|参数|值|说明|参数类型|数据类型|
|:---|:---|:---|:---|:---|
|api-version|1.0|客户端所请求的 API 的版本。 允许值包括：`1.0`。|query   |字符串|
|from|(empty)   |指定传入语音的语言。 值为一种语言标识符，来自语言 API 的响应中的 `speech` 范围。|query|字符串|
|to|(empty)|指定翻译转录文本时的目标语言。 值为一种语言标识符，来自语言 API 的响应中的 `text` 范围。|query|字符串|
|features|(empty)   |客户端选择的以逗号分隔的功能集。 可用功能包括：<ul><li>`TextToSpeech`：指定服务必须返回最终翻译语句的转换音频。</li><li>`Partial`：指定在音频正流式传输到服务的情况下，服务必须返回中间识别结果。</li><li>`TimingInfo`：指定服务必须返回与每个识别相关联的计时信息。</li></ul>例如，若要接收部分结果和文本转语音内容但不接收计时信息，客户端可以指定 `features=partial,texttospeech`。 请注意，最终结果始终会流式传输到客户端。|query|字符串|
|voice|(empty)|确保使用什么语音将已翻译文本从文本转换为语音。 值为一种语音标识符，来自语言 API 的响应中的 tts 范围。 如果未指定语音，则当文本转语音功能启用时，系统会自动选择一种语音。|query|字符串|
|格式|(empty)|指定服务返回的文本转语音音频流的格式。 可用选项包括：<ul><li>`audio/wav`：波形音频流。 客户端应使用 WAV 标头来正确解释音频格式。 文本转语音的 WAV 音频为 16 位单通道 PCM，采样频率为 24kHz 或 16kHz。</li><li>`audio/mp3`：MP3 音频流。</li></ul>默认为 `audio/wav`。|query|字符串|
|ProfanityAction    |(empty)    |指定服务应如何处理在语音中识别出的不雅内容。 有效措施包括：<ul><li>`NoAction`：按原样保留不雅内容。</li><li>`Marked`：将不雅内容替换为某个标记。 参见 `ProfanityMarker` 参数。</li><li>`Deleted`：删除不雅内容。 例如，如果系统将 `"jackass"` 一词视为不雅内容，则短语 `"He is a jackass."` 会变为 `"He is a .".`</li></ul>默认为 Marked。|query|字符串|
|ProfanityMarker|(empty)    |指定在将 `ProfanityAction` 设置为 `Marked` 时，如何处理检测到的不雅内容。 有效选项包括：<ul><li>`Asterisk`：将不雅内容替换为字符串 `***`。 例如，如果系统将 `"jackass"` 一词视为不雅内容，则短语 `"He is a jackass."` 会变为 `"He is a ***.".`</li><li>`Tag`：将不雅内容括在不雅内容 XML 标记中。 例如，如果系统将 `"jackass"` 一词视为不雅内容，则短语 `"He is a jackass."` 会变为 `"He is a <profanity>jackass</profanity>."`。</li></ul>默认为 `Asterisk`。|query|字符串|
|授权|(empty)  |指定客户端的持有者令牌的值。 使用前缀 `Bearer`，后跟身份验证令牌服务返回的 `access_token` 值。|标头的值开始缓存响应   |字符串|
|Ocp-Apim-Subscription-Key|(empty)|在 `Authorization` 标头未指定的情况下为必填项。|标头的值开始缓存响应|字符串|
|access_token|(empty)   |一种传递有效 OAuth 访问令牌的替代方式。 持有者令牌在提供时通常带有标头 `Authorization`。 某些 websocket 库不允许客户端代码设置标头。 在这种情况下，客户端可以使用 `access_token` 查询参数来传递有效的令牌。 使用访问令牌进行身份验证时，如果 `Authorization` 标头未设置，则必须设置 `access_token`。 如果标头和查询参数都设置，则会忽略查询参数。 客户端应该只使用一种方法来传递令牌。|query|字符串|
|subscription-key|(empty)   |一种传递订阅密钥的替代方式。 某些 websocket 库不允许客户端代码设置标头。 在这种情况下，客户端可以使用 `subscription-key` 查询参数来传递有效的订阅密钥。 使用订阅密钥进行身份验证时，如果 `Ocp-Apim-Subscription-Key` 标头未设置，则必须设置 subscription-key。 如果标头和查询参数都设置，则会忽略查询参数。 客户端应该只使用一种方法来传递 `subscription key`。|query|字符串|
|X-ClientTraceId    |(empty)    |客户端生成的 GUID，用于跟踪请求。 为了正确排查问题，客户端应为每个请求提供新值并记录该值。<br/>该值应使用查询参数 `X-ClientTraceId` 而不是标头来传递。 如果标头和查询参数都设置，则会忽略查询参数。|标头的值开始缓存响应|字符串|
|X-CorrelationId|(empty)    |一个客户端生成的标识符，用于将一个对话中的多个通道关联在一起。 可以创建多个语音翻译会话，以便在用户之间进行对话。 在这种情况下，所有语音翻译会话会使用同一相关 ID 将通道绑定在一起。 这样可以方便跟踪和诊断。 标识符应遵循的格式：`^[a-zA-Z0-9-_.]{1,64}$`<br/>该值应使用查询参数 `X-CorrelationId` 而不是标头来传递。 如果标头和查询参数都设置，则会忽略查询参数。|标头的值开始缓存响应|字符串|
|X-ClientVersion|(empty)    |标识客户端应用程序的版本。 示例：“2.1.0.123”。<br/>该值应使用查询参数 `X-ClientVersion` 而不是标头来传递。 如果标头和查询参数都设置，则会忽略查询参数。|标头的值开始缓存响应|字符串|
|X-OsPlatform|(empty)   |标识运行客户端应用程序的操作系统的名称和版本。 示例：“Android 5.0”、“iOs 8.1.3”、“Windows 8.1”。<br/>该值应使用查询参数 `X-OsPlatform` 而不是标头来传递。 如果标头和查询参数都设置，则会忽略查询参数。|标头的值开始缓存响应|字符串|

### <a name="response-messages"></a>响应消息

|HTTP 状态代码|原因|响应模型|标头|
|:--|:--|:--|:--|
|101    |WebSocket 升级。|模型示例值 <br/> Object {}|X-RequestId<br/>一个用于标识请求的值，目的是方便故障排除。<br/>字符串|
|400    |请求错误。 请检查输入参数以确保它们有效。 响应对象包括错误的更详细描述。|||
|401    |未授权。 确保凭据已设置并有效，同时你的 Azure 数据市场订阅处于正常状态并有余额可用。|||
|500    |发生错误。 如果错误仍然存在，请使用客户端跟踪标识符 (X-ClientTraceId) 或请求标识符 (X-RequestId) 进行报告。|||
|503    |服务器暂不可用。 请重试请求。 如果错误仍然存在，请使用客户端跟踪标识符 (X-ClientTraceId) 或请求标识符 (X-RequestId) 进行报告。|||

    


    





    
    




    




    




    

            




        









