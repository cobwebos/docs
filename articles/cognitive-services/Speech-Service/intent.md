---
title: 意向识别示例 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 此处为意向识别的示例。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213192"
---
# <a name="sample-for-intent-recognition"></a>意向识别示例

请先获取订阅密钥。 与认知服务语音 SDK 支持的其他服务相比，意向识别服务需要特定的订阅密钥。 [在此处](https://www.luis.ai)可找到有关意向识别技术的其他信息，以及有关如何获取订阅密钥的信息。 在示例中的适当位置，将内容替换为自己的语言理解订阅密钥、[订阅区域](regions.md)和意向模型的 AppId。

## <a name="top-level-declarations"></a>顶级声明

对于下述所有示例，应进行以下顶级声明：

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>使用麦克风进行意向识别

下方代码片段展示了如何使用默认语言 (`en-US`) 识别来自麦克风输入的意向。

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>使用指定语言通过麦克风进行意向识别

下方代码片段展示了如何使用指定语言（在本例中为德语 (`de-de`)）识别来自麦克风输入的意向。

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>使用事件从文件进行意向识别

代码片段显示了如何以连续的方式识别默认语言 (`en-US`) 中的意向。 此代码允许访问其他信息，例如中间结果。 输入来自音频文件，支持的格式是单通道（单声道）WAV/PCM，采样率为 16 KHz。

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>后续步骤

- [语音识别](./speech-to-text-sample.md)

- [翻译](./translation.md)
