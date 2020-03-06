---
title: 通过 Speech SDK-语音服务流式处理编解码器压缩音频
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 将压缩音频流式传输到语音服务。 适用于C++适用C#于 Linux 的、和 java、Android 中的 java 和 iOS 中的目标-C。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331104"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>在语音 SDK 中使用编解码器压缩的音频输入

语音 SDK 的**压缩音频输入流**API 提供了使用 PullStream 或 PushStream 将压缩音频流式传输到语音服务的方法。

> [!IMPORTANT]
> 目前，Linux （ubuntu 16.04、ubuntu C++18.04 C#、Debian 9、RHEL 8、CentOS 8）的、和 Java 支持流式传输的输入音频。 它[在 Android](how-to-use-codec-compressed-audio-input-streams-android.md)和[iOS 平台中](how-to-use-codec-compressed-audio-input-streams-ios.md)的适用于 Java 的 Java 也是支持的。
> 需要1.7.0 或更高版本的语音 SDK （RHEL 8、CentOS 8 的版本1.10.0 或更高版本）。

对于 wav/PCM，请参阅主线语音文档。  在 wav/PCM 外部，支持以下编解码器压缩输入格式：

- MP3
- OPUS/OGG
- FLAC
- Wav 容器中的 ALAW
- Wav 容器中的 MULAW

## <a name="prerequisites"></a>先决条件

处理压缩音频是使用[适用 gstreamer](https://gstreamer.freedesktop.org)实现的。 出于许可原因，适用 gstreamer 不编译二进制文件并将其与语音 SDK 链接。 因此，应用程序开发人员需要在18.04、16.04 和 Debian 9 上安装以下各项，才能使用压缩的输入音频。

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

在 RHEL/CentOS 8 上：

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> 在 RHEL/CentOS 8 上，按照[如何为 Linux 配置 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)的说明进行操作。

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

若要以压缩的音频格式流式传输到语音服务，请创建 `PullAudioInputStream` 或 `PushAudioInputStream`。 然后，通过指定流的压缩格式，从 stream 类的实例创建 `AudioConfig`。

假设你有一个名为 `myPushStream` 的输入流类，并使用 OPUS/OGG。 代码可能如下所示：

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
* [请参阅如何在 Java 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
