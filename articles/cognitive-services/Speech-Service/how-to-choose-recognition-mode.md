---
title: 使用 Speech SDK 选择语音识别模式
titleSuffix: Azure Cognitive Services
description: 了解如何在使用 Speech SDK 时选择最佳的识别模式。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 51bf005bdad4197120fed96894ac1cdd150738ee
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75935223"
---
# <a name="choose-a-speech-recognition-mode"></a>选择语音识别模式

考虑语音到文本识别操作时，[语音 SDK](speech-sdk.md)提供多种模式来处理语音。 从概念上讲，有时称为*识别模式*。 本文比较了各种识别模式。

## <a name="recognize-once"></a>识别一次

如果要一次处理每个查询文本一个 "句子"，请使用 "识别一次" 函数。 此方法将在从检测到的语音开始之前的输入中检测识别的查询文本，直到下一次暂停。 通常，暂停会标记句尾或想象的行。

在一个可识别的查询文本结束时，服务将停止处理来自该请求的音频。 识别的最大限制为每秒20秒。

::: zone pivot="programming-language-csharp"

有关使用 `RecognizeOnceAsync` 函数的详细信息，请参阅[.Net SPEECH SDK 文档](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)。

```csharp
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
```

::: zone-end
::: zone pivot="programming-language-cpp"

有关使用 `RecognizeOnceAsync` 函数的详细信息，请参阅[ C++语音 SDK 文档](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)。

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

有关使用 `recognizeOnceAsync` 函数的详细信息，请参阅[Java SPEECH SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)。

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

有关使用 `recognize_once` 函数的详细信息，请参阅[Python 语音 SDK 文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)。

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="continuous"></a>连续

如果需要长时间运行的识别，请使用启动和相应的停止功能进行连续识别。 Start 函数将启动并继续处理所有最谈话，直到调用 stop 函数或过多的静默时间为止。 使用连续模式时，请确保注册到发生事件时将触发的各种事件。 例如，当发生语音识别时，将触发 "已识别" 事件。 需要有一个事件处理程序来处理识别。 每个会话由语音服务强制执行的总语音识别时间限制为10分钟。

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
await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
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
::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="dictation"></a>听写

使用连续识别时，可以使用相应的 "启用听写" 功能启用听写处理。 此模式将导致语音配置实例解释句子结构（如标点符号）的单词说明。 例如，"您居住于城镇问号" 的 "查询文本" 将被解释为文本 "您居住在城镇中"。

::: zone pivot="programming-language-csharp"

有关使用 `EnableDictation` 函数的详细信息，请参阅[.Net SPEECH SDK 文档](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)。

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有关使用 `EnableDictation` 函数的详细信息，请参阅[ C++语音 SDK 文档](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)。

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

有关使用 `enableDictation` 函数的详细信息，请参阅[Java SPEECH SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)。

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

有关使用 `enable_dictation` 函数的详细信息，请参阅[Python 语音 SDK 文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)。

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 GitHub 上的其他语音 SDK 示例](https://aka.ms/csspeech/samples)
