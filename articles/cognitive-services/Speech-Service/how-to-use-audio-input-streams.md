---
title: 语音 SDK 音频输入流概念
titleSuffix: Azure Cognitive Services
description: 语音 SDK 的音频输入流 API 功能概述。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109931"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>语音 SDK 的音频输入流 API 简介

语音 SDK 的**音频输入流**API 提供了一种将音频流式传输到识别器的方法，而不是使用麦克风或输入文件 api。

使用音频输入流时需按以下步骤操作：

- 识别音频流的格式。 格式必须受语音 SDK 和语音服务支持。 目前仅支持以下配置：

  PCM 格式的音频样本、一个频道、每秒 16000 次采样、每秒 32000 字节、两个块对齐、（16 位，包括样本的内边距）、每个样本 16 位。

  SDK 中用于创建音频格式的相应代码如下所示：

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- 请确保代码可根据上述规格提供 RAW 音频数据。 如果音频源数据不符合支持的格式，则必须将音频转码为所需格式。

- 自行创建派生自 `PullAudioInputStreamCallback` 的音频输入流类。 实现 `Read()` 和 `Close()` 元素。 确切的函数签名取决于语言，但代码可能与如下代码示例类似：

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- 根据音频格式和输入流创建音频配置。 创建识别器时同时传入常规语音配置和音频输入配置。 例如：

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [了解如何在 C# 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
