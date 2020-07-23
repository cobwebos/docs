---
title: 如何指定语音转文本的源语言
titleSuffix: Azure Cognitive Services
description: 有了语音 SDK，就可以在将语音转换为文本时指定源语言。 本文介绍如何使用 FromConfig 和 SourceLanguageConfig 方法让语音服务知道源语言并提供自定义模型目标。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 32c08af129172fb1dbebf1679ea01694e8bd3d1a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653280"
---
# <a name="specify-source-language-for-speech-to-text"></a>指定语音转文本的源语言

本文介绍如何指定某个传递给语音 SDK 进行语音识别的音频输入的源语言。 另外还提供示例代码，用于指定自定义语音识别模型以改进识别。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>如何在 C# 中指定源语言

在此示例中，使用 `SpeechRecognizer` 构造将源语言显式作为参数提供。

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此示例中，源语言是使用 `SourceLanguageConfig` 提供的。 然后，将 `sourceLanguageConfig` 作为参数传递给 `SpeechRecognizer` 构造。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，源语言和自定义终结点是使用 `SourceLanguageConfig` 提供的。 然后，将 `sourceLanguageConfig` 作为参数传递给 `SpeechRecognizer` 构造。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> 在 C# 中，`SpeechRecognitionLanguage` 和 `EndpointId` set 方法已从 `SpeechConfig` 类中弃用。 建议不要使用这些方法，在构造 `SpeechRecognizer` 时不应使用它们。

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>如何在 C++ 中指定源语言

在此示例中，源语言是使用 `FromConfig` 方法作为参数显式提供的。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

在此示例中，源语言是使用 `SourceLanguageConfig` 提供的。 然后，在创建 `recognizer` 时，`sourceLanguageConfig` 会作为参数传递给 `FromConfig`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，源语言和自定义终结点是使用 `SourceLanguageConfig` 提供的。 在创建 `recognizer` 时，`sourceLanguageConfig` 作为参数传递给 `FromConfig`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> 在 C++ 和 Java 中，`SetSpeechRecognitionLanguage` 和 `SetEndpointId` 是 `SpeechConfig` 类中弃用的方法。 建议不要使用这些方法，在构造 `SpeechRecognizer` 时不应使用它们。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>如何在 Java 中指定源语言

在此示例中，源语言是在创建新的 `SpeechRecognizer` 时显式提供的。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此示例中，源语言是使用 `SourceLanguageConfig` 提供的。 然后，在创建新的 `SpeechRecognizer` 时，`sourceLanguageConfig` 会作为参数传递。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，源语言和自定义终结点是使用 `SourceLanguageConfig` 提供的。 然后，在创建新的 `SpeechRecognizer` 时，`sourceLanguageConfig` 会作为参数传递。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> 在 C++ 和 Java 中，`setSpeechRecognitionLanguage` 和 `setEndpointId` 是 `SpeechConfig` 类中弃用的方法。 建议不要使用这些方法，在构造 `SpeechRecognizer` 时不应使用它们。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>如何在 Python 中指定源语言

在此示例中，使用 `SpeechRecognizer` 构造将源语言显式作为参数提供。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

在此示例中，源语言是使用 `SourceLanguageConfig` 提供的。 然后，将 `SourceLanguageConfig` 作为参数传递给 `SpeechRecognizer` 构造。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

在此示例中，源语言和自定义终结点是使用 `SourceLanguageConfig` 提供的。 然后，将 `SourceLanguageConfig` 作为参数传递给 `SpeechRecognizer` 构造。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> 在 Python 中，`SpeechConfig` 类中的 `speech_recognition_language` 和 `endpoint_id` 属性已弃用。 建议不要使用这些属性，在构造 `SpeechRecognizer` 时不应使用它们。

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>如何在 Javascript 中指定源语言

第一步是创建 `SpeechConfig`：

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

接下来，使用 `speechRecognitionLanguage` 指定音频的源语言：

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

如果使用自定义模型进行识别，则可使用 `endpointId` 指定终结点：

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>如何在 Objective-C 中指定源语言

在此示例中，使用 `SPXSpeechRecognizer` 构造将源语言显式作为参数提供。

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

在此示例中，源语言是使用 `SPXSourceLanguageConfiguration` 提供的。 然后，将 `SPXSourceLanguageConfiguration` 作为参数传递给 `SPXSpeechRecognizer` 构造。

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

在此示例中，源语言和自定义终结点是使用 `SPXSourceLanguageConfiguration` 提供的。 然后，将 `SPXSourceLanguageConfiguration` 作为参数传递给 `SPXSpeechRecognizer` 构造。

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage`和 `endpointId` 属性已从 `SPXSpeechConfiguration` 目标为 C 的类中弃用。 建议不要使用这些属性，在构造 `SPXSpeechRecognizer` 时不应使用它们。

::: zone-end

## <a name="see-also"></a>另请参阅

* 如需语音转文本支持的语言和区域设置的列表，请参阅[语言支持](language-support.md)。

## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)