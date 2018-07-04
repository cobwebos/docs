---
title: 语音转文本示例 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 以下是语音转文本的示例。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030244"
---
# <a name="sample-for-speech-to-text"></a>语音转文本示例

> [!NOTE]
> 有关此示例及其他示例的下载说明，请参阅[语音 SDK 示例](samples.md)。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 对于下述所有示例，应进行以下顶级声明：
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>使用麦克风进行语音识别

下方代码片段展示了如何从麦克风识别默认语言 (`en-US`) 的语音输入。

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>使用文件进行语音识别

以下代码片段从音频文件中识别默认语言 (`en-US`) 的语音输入，支持的格式为单通道（单声道）WAV/PCM，采样率为 16 KHz。

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>使用自定义模型进行语音识别

[自定义语音服务 (CRIS)](https://www.cris.ai/)允许自定义应用程序的 Microsoft 的语音转文本引擎。 以下代码片段演示如何使用 CRI 模型识别来自麦克风的语音；在运行该语音前，请填写 CRI 订阅密钥和你自己的部署标识。

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>连续语音识别

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>示例源代码

最新版本和更高级的示例位于专用的 [GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="next-steps"></a>后续步骤

- [意向识别](./intent.md)

- [翻译](./translation.md)
