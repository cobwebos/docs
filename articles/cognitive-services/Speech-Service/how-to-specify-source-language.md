---
title: 如何指定语音转换到文本的源语言
titleSuffix: Azure Cognitive Services
description: 语音 SDK 允许在将语音转换为文本时指定源语言。 本文介绍如何使用 FromConfig 和 SourceLanguageConfig 方法让语音服务知道源语言，并提供自定义模型目标。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e4f4dd3c1e23855a8a1a69dac72c232779206f1d
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121703"
---
# <a name="specify-source-language-for-speech-to-text"></a>指定语音转换到文本的源语言

本文介绍如何为传递到语音 SDK for speech 识别的音频输入指定源语言。 此外，还提供了示例代码来指定自定义语音模型，以改善识别。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>如何在中指定源语言C#

在此示例中，使用 `SpeechRecognizer` 构造将源语言显式作为参数提供。

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此示例中，使用 `SourceLanguageConfig`提供源语言。 然后，将 `sourceLanguageConfig` 作为参数传递给 `SpeechRecognizer` 构造。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，使用 `SourceLanguageConfig`提供源语言和自定义终结点。 然后，将 `sourceLanguageConfig` 作为参数传递给 `SpeechRecognizer` 构造。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` 和 `EndpointId` set 方法在中的 `SpeechConfig` 类中C#已弃用。 不建议使用这些方法，因此在构造 `SpeechRecognizer`时不应使用这些方法。

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>如何在中指定源语言C++

在此示例中，使用 `FromConfig` 方法将源语言显式作为参数提供。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

在此示例中，使用 `SourceLanguageConfig`提供源语言。 然后，在创建 `recognizer`时，将 `sourceLanguageConfig` 作为参数传递给 `FromConfig`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，使用 `SourceLanguageConfig`提供源语言和自定义终结点。 创建 `recognizer`时，将 `sourceLanguageConfig` 作为参数传递给 `FromConfig`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` 和 `SetEndpointId` 是和 Java 中C++ `SpeechConfig` 类的不推荐使用的方法。 不建议使用这些方法，因此在构造 `SpeechRecognizer`时不应使用这些方法。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>如何在 Java 中指定源语言

在此示例中，创建新的 `SpeechRecognizer`时，会显式提供源语言。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此示例中，使用 `SourceLanguageConfig`提供源语言。 然后，在创建新 `SpeechRecognizer`时，将 `sourceLanguageConfig` 作为参数传递。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此示例中，使用 `SourceLanguageConfig`提供源语言和自定义终结点。 然后，在创建新 `SpeechRecognizer`时，将 `sourceLanguageConfig` 作为参数传递。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` 和 `setEndpointId` 是和 Java 中C++ `SpeechConfig` 类的不推荐使用的方法。 不建议使用这些方法，因此在构造 `SpeechRecognizer`时不应使用这些方法。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>如何在 Python 中指定源语言

第一步是创建 `speech_config`：

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

接下来，指定 `speech_recognition_language`的音频源语言：

```Python
speech_config.speech_recognition_language="de-DE"
```

如果要使用自定义模型进行识别，则可以使用 `endpoint_id`指定终结点：

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>如何在 Javascript 中指定源语言

第一步是创建 `SpeechConfig`：

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

接下来，指定 `speechRecognitionLanguage`的音频源语言：

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

如果要使用自定义模型进行识别，则可以使用 `endpointId`指定终结点：

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>如何在目标中指定源语言-C

第一步是创建 `speechConfig`：

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

接下来，指定 `speechRecognitionLanguage`的音频源语言：

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

如果要使用自定义模型进行识别，则可以使用 `endpointId`指定终结点：

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>另请参阅

* 有关语音转换到文本的支持的语言和区域设置的列表，请参阅[语言支持](language-support.md)。

## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)
