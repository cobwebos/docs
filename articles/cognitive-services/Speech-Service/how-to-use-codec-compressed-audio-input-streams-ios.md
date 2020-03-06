---
title: 在 iOS 上通过语音 SDK 流式处理编解码器压缩音频
titleSuffix: Azure Cognitive Services
description: 了解如何在 iOS 上通过语音 SDK 将压缩音频流式传输到语音服务。
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 5d65ac83fc8a41008971108c5b370c1d0416113b
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331513"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>如何：在 iOS 上对 Speech SDK 使用编解码器压缩的音频输入

语音 SDK 的**压缩音频输入流**API 提供了一种使用请求或推送流将压缩音频流式传输到语音服务的方法。

> [!IMPORTANT]
> 在 iOS 上流式传输压缩音频需要语音 SDK 版本1.7.0 或更高版本。 [ C++Linux 上的C#、和 java （ubuntu 16.04、ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）](how-to-use-codec-compressed-audio-input-streams.md)和 Android 上的 java 也支持[。](how-to-use-codec-compressed-audio-input-streams-android.md)

对于 wav/PCM，请参阅主线语音文档。 在 wav/PCM 外部，支持以下编解码器压缩输入格式：

- MP3
- OPUS/OGG
- FLAC
- Wav 容器中的 ALAW
- Wav 容器中的 MULAW

## <a name="prerequisites"></a>必备条件

处理压缩音频是使用[适用 gstreamer](https://gstreamer.freedesktop.org)实现的。 出于许可原因，这些功能不能随 SDK 一起提供，但包含这些函数的包装库需要由应用程序开发人员构建并使用 SDK 随应用一起提供。

若要生成此包装库，请首先下载并安装[适用 GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然后，下载[包装库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的 Xcode 项目。

在 Xcode 中打开项目，并为**通用 IOS 设备**目标生成该项目--对于特定目标，它将不起作用。

生成步骤将生成动态框架包，其中包含具有 `GStreamerWrapper.framework`名称的所有必需体系结构的动态库。

此框架必须包含在通过语音服务 SDK 使用压缩音频流的所有应用中。

若要完成此操作，请在 Xcode 项目中应用以下设置：

1. 将刚刚生成的 `GStreamerWrapper.framework` 和认知服务语音 SDK 的框架（可从[此处](https://aka.ms/csspeech/iosbinary)下载到包含示例项目的目录）复制到其中。
1. 在_项目设置_中调整框架的路径。
   1. 在 "**常规**" 选项卡的 "**嵌入的二进制文件**标头" 下，将 SDK 库添加为框架：**添加嵌入的二进制文件** > **添加其他 ...** > 导航到所选的目录，并选择两个框架。
   1. 转到“生成设置”选项卡，激活“所有”设置。
1. 将目录 `$(SRCROOT)/..` 添加到“搜索路径”标头下的“框架搜索路径”。

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

若要以压缩的音频格式流式传输到语音服务，请创建 `SPXPullAudioInputStream` 或 `SPXPushAudioInputStream`。

以下代码片段演示了如何从 `SPXPushAudioInputStream`的实例创建 `SPXAudioConfiguration`，并指定 mp3 作为流的压缩格式。

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

下一段代码段显示了如何从文件中读取压缩的音频数据并抽取到 `SPXPushAudioInputStream`中。

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [请参阅如何在 Java 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
