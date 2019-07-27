---
title: 通过 Speech SDK-语音服务流式处理编解码器压缩音频
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 将压缩音频流式传输到 Azure 语音服务。 适用于C++Linux C#的、和 Java。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559558"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>在语音 SDK 中使用编解码器压缩的音频输入

语音 SDK 的**压缩音频输入流**API 提供了使用 PullStream 或 PushStream 将压缩音频流式传输到语音服务的方法。

> [!IMPORTANT]
> 仅 Linux (ubuntu 16.04、ubuntu C++18.04 C#、Debian 9) 上的、和 Java 支持流式压缩音频。
> 需要语音 SDK 版本1.4.0 或更高版本。

对于 wav/PCM, 请参阅主线语音文档。  在 wav/PCM 外部, 支持以下编解码器压缩输入格式:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的先决条件

安装这些附加的依赖项, 以便在适用于 Linux 的语音 SDK 中使用压缩的音频输入:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

若要以压缩的音频格式流式传输到语音服务, `PullAudioInputStream`请`PushAudioInputStream`创建或。 然后, `AudioConfig`通过指定流的压缩格式, 从 stream 类的实例创建。

假设你有一个名`myPushStream`为的输入流类, 并使用 OPUS/OGG。 你的代码可能如下所示:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
