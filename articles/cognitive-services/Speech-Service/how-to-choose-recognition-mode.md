---
title: 使用语音 SDK 选择语音识别模式
titleSuffix: Azure Cognitive Services
description: 了解如何在使用语音 SDK 时选择最佳识别模式。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d997cb592d9d648998f2b44d9f61f465f05faeb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79079821"
---
# <a name="choose-a-speech-recognition-mode"></a>选择语音识别模式

在考虑语音到文本识别操作时，[语音 SDK](speech-sdk.md)提供了多种处理语音的模式。 从概念上讲，有时称为*识别模式*。 本文比较了各种识别模式。

## <a name="recognize-once"></a>识别一次

如果要一次处理一个"句子"，请使用"识别一次"功能。 此方法将从检测到的语音开始，直到下一次暂停，从输入中检测识别的说出。 通常，暂停标志着句子或思路的结束。

在一个可识别的话语结束时，服务将停止处理来自该请求的音频。 识别的最大限制是 20 秒的句子持续时间。

::: zone pivot="programming-language-csharp"

有关使用 函数`RecognizeOnceAsync`的详细信息，请参阅[.NET 语音 SDK 文档](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)。

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有关使用 函数`RecognizeOnceAsync`的详细信息，请参阅[C++语音 SDK 文档](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)。

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

有关使用 函数`recognizeOnceAsync`的详细信息，请参阅 Java[语音 SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)。

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

有关使用 函数`recognize_once`的详细信息，请参阅 Python[语音 SDK 文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)。

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="continuous"></a>连续

如果需要长时间运行的识别，请使用启动和相应的停止函数进行连续识别。 start 函数将开始并继续处理所有陈述，直到调用停止函数，或直到超过太多时间在沉默中过去。 使用连续模式时，请确保注册到发生时将触发的各种事件。 例如，当发生语音识别时，将触发"识别"事件。 您需要有一个事件处理程序来处理识别。

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
::: zone pivot="programming-language-more"

有关其他语言，请参阅[语音 SDK 参考文档](speech-to-text.md#speech-sdk-reference-docs)。

::: zone-end

## <a name="dictation"></a>听写

使用连续识别时，可以使用相应的"启用听写"功能启用听写处理。 此模式将导致语音配置实例解释句子结构（如标点符号）的单词描述。 例如，"你住在城里问号"的话语将被解释为"你住在城里吗？

::: zone pivot="programming-language-csharp"

有关使用 函数`EnableDictation`的详细信息，请参阅[.NET 语音 SDK 文档](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)。

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

有关使用 函数`EnableDictation`的详细信息，请参阅[C++语音 SDK 文档](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)。

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

有关使用 函数`enableDictation`的详细信息，请参阅 Java[语音 SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)。

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

有关使用 函数`enable_dictation`的详细信息，请参阅 Python[语音 SDK 文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)。

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
> [在 GitHub 上浏览其他语音 SDK 示例](https://aka.ms/csspeech/samples)
