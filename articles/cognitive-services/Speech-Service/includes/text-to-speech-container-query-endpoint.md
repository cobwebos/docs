---
title: 查询文本到语音容器终结点
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 7e5ea8dcddce31a414d983d14fba483eb388d5d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334663"
---
容器提供 [基于 REST 的终结点 api](../rest-text-to-speech.md)。 有许多适用于平台、框架和语言变体的 [示例源代码项目](https://github.com/Azure-Samples/Cognitive-Speech-TTS) 。

对于标准或神经文本到语音转换容器，你应依赖于所下载图像标记的区域设置和语音。 例如，如果下载了标记，则 `latest` 默认区域设置为 `en-US` 和 `AriaRUS` 语音。 `{VOICE_NAME}`然后，参数将为 [`en-US-AriaRUS`](../language-support.md#standard-voices) 。 请参阅下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

但是，对于*自定义的文本到语音转换*，需要从[自定义语音门户](https://aka.ms/custom-voice-portal)获取**语音/模式**。 自定义模型名称与语音名称同义。 导航到 **定型** 页面，并复制要用作参数的 **语音/模型** `{VOICE_NAME}` 。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自定义语音模型-语音名称":::

请参阅下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

让我们构造一个 HTTP POST 请求，提供一些标头和数据负载。 将 `{VOICE_NAME}` 占位符替换为自己的值。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


此命令：

* 为终结点构造 HTTP POST 请求 `speech/synthesize/cognitiveservices/v1` 。
* 指定 `Accept` 标头 `audio/*`
* 指定的 `Content-Type` 标头 `application/ssml+xml` ，有关详细信息，请参阅 [请求正文](../rest-text-to-speech.md#request-body)。
* 指定的 `X-Microsoft-OutputFormat` 标头 `riff-16khz-16bit-mono-pcm` ，有关更多选项，请参阅 [音频输出](../rest-text-to-speech.md#audio-outputs)。
* 将给定的 [ (SSML) 请求的语音合成标记语言 ](../speech-synthesis-markup.md) 发送 `{VOICE_NAME}` 到终结点。