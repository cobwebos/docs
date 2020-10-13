---
title: 如何跟踪语音 SDK 内存使用情况 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务 SDK 支持多种编程语言，可用于语音到文本和文本到语音转换，以及语音翻译。 本文讨论了 SDK 中内置的内存管理工具。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934134"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>如何跟踪语音 SDK 内存使用情况

语音 SDK 基于一个本机代码库，该库可以通过一系列互操作性层投影成多种编程语言。 每种特定于语言的投影都有从从习惯上来说是正确的功能，用于管理对象生命周期。 此外，语音 SDK 还包括具有对象日志记录功能和对象限制的内存管理工具，用于跟踪资源使用情况。 

## <a name="how-to-read-object-logs"></a>如何读取对象日志

如果[启用了语音 SDK 日志记录](how-to-use-logging.md)，则会发出跟踪标记来启用历史对象观察。 这些标记包括： 

* `TrackHandle` 或 `StopTracking` 
* 对象类型
* 受跟踪的对象的数目、对象的类型，以及当前受跟踪的数目。

下面是一个示例日志： 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>设置警告阈值

可以选择创建警告阈值。如果超出该阈值（假定启用了日志记录），则会记录一条警告消息。 警告消息包含所有存在的对象及其计数的转储。 可以通过此信息更好地了解问题。 

若要启用警告阈值，必须在 `SpeechConfig` 对象上指定它。 创建新的识别器时，将检查此对象。 在下面的示例中，假设已创建一个名为 `config` 的 `SpeechConfig` 实例：

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> 此属性的默认值是 10,000。

## <a name="set-an-error-threshold"></a>设置错误阈值 

使用语音 SDK，可以设置在给定时间允许的最大对象数。 如果启用此设置，则达到最大数目时，将无法尝试创建新的识别器对象。 现有对象将继续发挥作用。

下面是一个示例错误：

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

若要启用错误阈值，必须在 `SpeechConfig` 对象上指定它。 创建新的识别器时，将检查此对象。 在下面的示例中，假设已创建一个名为 `config` 的 `SpeechConfig` 实例：

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> 对于 `size_t` 数据类型，此属性的默认值为特定于平台的最大值。 典型识别会消耗 7 到 10 个内部对象。

## <a name="next-steps"></a>后续步骤

* [详细了解语音 SDK](speech-sdk.md)