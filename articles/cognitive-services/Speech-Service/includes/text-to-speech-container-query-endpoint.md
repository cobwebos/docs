---
title: 查询文本到语音容器终结点
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491122"
---
容器提供[基于 REST 的终结点 api](../rest-text-to-speech.md)。 有许多适用于平台、框架和语言变体的[示例源代码项目](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)。

对于*标准的文本到语音转换*容器，你应依赖于所下载图像标记的区域设置和语音。 例如，如果您下载了 `latest` 标记，则默认区域设置为 `en-US` 并 `JessaRUS` 语音。 然后，`{VOICE_NAME}` 参数[`en-US-JessaRUS`](../language-support.md#standard-voices)。 请参阅下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

但是，对于*自定义的文本到语音转换*，需要从[自定义语音门户](https://aka.ms/custom-voice-portal)获取**语音/模式**。 自定义模型名称与语音名称同义。 导航到 "**培训**" 页，并复制**语音/模型**以用作 `{VOICE_NAME}` 参数。
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
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

此命令：

* 为 `speech/synthesize/cognitiveservices/v1` 终结点构造 HTTP POST 请求。
* 指定 `Accept` 标头 `audio/*`
* 指定 `application/ssml+xml`的 `Content-Type` 标头。有关详细信息，请参阅[请求正文](../rest-text-to-speech.md#request-body)。
* 指定 `riff-16khz-16bit-mono-pcm`的 `X-Microsoft-OutputFormat` 标头，有关更多选项，请参阅[音频输出](../rest-text-to-speech.md#audio-outputs)。
* 将给定 `{VOICE_NAME}` 的[语音合成标记语言（SSML）](../speech-synthesis-markup.md)请求发送到终结点。