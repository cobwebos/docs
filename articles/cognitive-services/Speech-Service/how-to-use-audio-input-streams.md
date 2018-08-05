---
title: AudioInputStream 概念
description: AudioInputStream API 的功能概述。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: b3e12fbc616c8d67b557102c6094467e119a23f1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281899"
---
# <a name="about-the-audio-input-stream-api"></a>关于音频输入流 API

音频输入流 API 可使流式音频流入识别器，无需使用麦克风或输入文件 API。

## <a name="api-overview"></a>API 概述

API 使用两个组件，`AudioInputStream`（原始音频数据）和 `AudioInputStreamFormat`。

`AudioInputStreamFormat` 定义音频数据的格式。 它相当于 Windows 上波形文件的标准 `WAVEFORMAT` 结构。

  - `FormatTag`

    音频的格式。 语音 SDK 目前仅支持 `format 1` (PCM - little-endian)。

  - `Channels`

    频道数量。 当前的语音服务仅支持单频道（单声道）音频材料。

  - `SamplesPerSec`

    采样率。 典型的麦克风录制每秒可进行 16000 次采样。

  - `AvgBytesPerSec`

    每秒平均字节数，计算公式为 `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`。 使用可变比特率的音频流的每秒平均字节数可能有所不同。

  - `BlockAlign`

    单帧大小，计算公式为 `Channels * ceil(wBitsPerSample, 8)`。 由于存在衬距，实际值可能高于此值。

  - `BitsPerSample`

    每个样本的位数。 典型的音频流对每个样本使用 16 位（CD 质量）。

自定义流适配器将替代 `AudioInputStream` 基类。 此适配器必须实现以下函数：

   - `GetFormat()`

     调用此函数可获取音频流的格式。 它获取一个指向 AudioInputStreamFormat 缓冲区的指针。

   - `Read()`

     调用此函数可从音频流获取数据。 第一个参数是一个指向为其中复制音频数据的缓冲区的指针。 第二个参数是缓冲区的大小。 此函数返回已复制到缓冲区的字节数。 返回值 `0` 指示流已结束。

   - `Close()`

     调用此函数可关闭音频流。

## <a name="usage-examples"></a>用法示例

一般情况下，使用音频输入流时涉及以下步骤：

  - 识别音频流的格式。 格式必须受 SDK 和语音服务支持。 目前支持以下配置：

    一个音频格式标记 (PCM)、一个频道、每秒 16000 次采样、每秒 32000 字节、两个块对齐、（16 位，包括样本的衬距）、每个样本 16 位

  - 请确保代码可按上述规格提供原始音频数据。 如果音频源数据不符合支持的格式，则必须将音频转码为所需格式。

  - 从 `AudioInputStream` 派生自定义音频输入流类。 执行 `GetFormat()`、`Read()` 和 `Close()` 操作。 确切的函数签名取决于语言，但代码可能与此代码示例类似：

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - 使用音频输入流：

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - 在某些语言中，必须在识别完成后显式删除 `contosoStream`。 不能在读取完整输入前发布 AudioStream。 使用 `StopContinuousRecognitionAsync` 和 `StopContinuousRecognitionAsync` 的方案中需要此示例中说明的概念：

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
