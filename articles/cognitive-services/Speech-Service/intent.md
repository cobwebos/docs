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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045004"
---
# <a name="sample-for-intent-recognition"></a>意向识别示例

> [!NOTE]
> 有关此示例及其他示例的下载说明，请参阅[语音 SDK 示例](samples.md)。

> [!NOTE]
> 请先获取订阅密钥。 与认知服务语音 SDK 支持的其他服务相比，意向识别服务需要特定的订阅密钥。 [在此处](https://www.luis.ai)可找到有关意向识别技术的其他信息，以及有关如何获取订阅密钥的信息。 在示例中的适当位置，将内容替换为你自己的订阅密钥、服务区域和意向模型的 AppId。

> [!NOTE]
> 对于下述所有示例，应进行以下顶级声明：
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>使用麦克风进行意向识别

下方代码片段展示了如何使用默认语言 (`en-US`) 识别来自麦克风输入的意向。

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>使用指定语言通过麦克风进行意向识别

下方代码片段展示了如何使用指定语言（在本例中为德语 (`de-de`)）识别来自麦克风输入的意向。

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>使用文件进行意向识别

下方代码片段以默认语言 (`en-US`) 识别音频文件中的意向，支持的格式为单通道（单声道）WAV/PCM，采样率 16 KHz。

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>使用事件进行意向识别

代码片段显示了如何以连续的方式识别意向。 此代码允许访问其他信息，例如中间结果。 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>示例源代码

有关最新的示例集，请参阅[认知服务语音 SDK 示例 GitHub 存储库 ](https://aka.ms/csspeech/samples)。

## <a name="next-steps"></a>后续步骤

- [语音识别](./speech-to-text-sample.md)

- [翻译](./translation.md)
