---
title: 选择使用语音 SDK 时的语音识别模式
titleSuffix: Azure Cognitive Services
description: 了解如何在使用语音 SDK 时选择最佳识别模式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1955e29eb1bef7fe6c8f57fa5d411c5c98faf58b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322761"
---
# <a name="choose-a-speech-recognition-mode"></a>选择语音识别模式

考虑语音转文本识别操作时，可以使用[语音 SDK](speech-sdk.md) 提供的多种模式来处理语音。 此类模式有时在概念上称为识别模式  。 本文比较各种识别模式。

## <a name="recognize-once"></a>识别一次

如果希望在处理每个言语时一次处理一个“句子”，请使用“识别一次”功能。 此方法会检测输入中的已识别言语，该输入从检测到的语音的开头开始，直到下一次暂停。 通常，暂停表示某个句子或思路结束。

在一个可识别的言语结束时，服务会停止处理来自该请求的音频。 识别的最大限制是一个句子的持续时间，即 20 秒。

::: zone pivot="programming-language-csharp"

有关如何使用 `RecognizeOnceAsync` 函数的详细信息，请参阅 [.NET 语音 SDK 文档](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)。

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有关如何使用 `RecognizeOnceAsync` 函数的详细信息，请参阅 [C++ 语音 SDK 文档](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)。

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

有关如何使用 `recognizeOnceAsync` 函数的详细信息，请参阅 [Java 语音 SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)。

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

有关如何使用 `recognize_once` 函数的详细信息，请参阅 [Python 语音 SDK 文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)。

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

若要详细了解如何使用 `recognizeOnceAsync` 函数，请参阅[适用于 JavaScript 的语音 SDK 文档](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-)。

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="continuous"></a>连续

如果需要长时间运行的识别，请使用启动函数和相应的停止函数进行连续识别。 启动函数会启动并持续处理所有言语，直到你调用停止函数或者静默时间过长为止。 使用连续模式时，请确保注册到进行语音识别时会触发的各种事件。 例如，进行语音识别时，会触发“已识别”事件。 需要有一个事件处理程序来处理识别。

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="dictation"></a>听写

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

::: zone pivot="programming-language-csharp"

有关如何使用 `EnableDictation` 函数的详细信息，请参阅 [.NET 语音 SDK 文档](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)。

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有关如何使用 `EnableDictation` 函数的详细信息，请参阅 [C++ 语音 SDK 文档](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)。

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

有关如何使用 `enableDictation` 函数的详细信息，请参阅 [Java 语音 SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)。

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

有关如何使用 `enable_dictation` 函数的详细信息，请参阅 [Python 语音 SDK 文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)。

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

若要详细了解如何使用 `enableDictation` 函数，请参阅[适用于 JavaScript 的语音 SDK 文档](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--)。

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 GitHub 上的其他语音 SDK 示例](https://aka.ms/csspeech/samples)
