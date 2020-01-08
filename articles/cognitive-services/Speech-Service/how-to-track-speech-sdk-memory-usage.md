---
title: 如何跟踪语音 SDK 内存使用情况-语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务 SDK 支持多种编程语言，用于语音到文本和文本到语音转换，以及语音翻译。 本文讨论了 SDK 中内置的内存管理工具。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456428"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>如何跟踪语音 SDK 内存使用情况

语音 SDK 基于一系列互操作性层投影到多种编程语言的本机代码库。 每种语言特定的投影都具有 idiomatically 的正确功能来管理对象生命周期。 此外，语音 SDK 还包括内存管理工具，用于跟踪使用对象日志记录和对象限制的资源使用情况。 

## <a name="how-to-read-object-logs"></a>如何读取对象日志

如果[启用了语音 SDK 日志记录](how-to-use-logging.md)，则会发出跟踪标记以启用历史对象观察。 这些标记包括： 

* `TrackHandle` 或 `StopTracking` 
* 对象类型
* 要跟踪的对象的类型以及当前正在跟踪的对象的数目。

下面是一个示例日志： 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>设置警告阈值

你可以选择创建警告阈值，如果超出该阈值（假定启用了日志记录），则会记录一条警告消息。 警告消息包含所有存在的对象及其计数的转储。 此信息可用于更好地了解问题。 

若要启用警告阈值，必须在 `SpeechConfig` 对象上指定它。 创建新的识别器时，将检查此对象。 在下面的示例中，假设已创建一个名为 `config``SpeechConfig` 实例：

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
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> 此属性的默认值为10000。

## <a name="set-an-error-threshold"></a>设置错误阈值 

使用 Speech SDK，可以设置给定时间内允许的最大对象数。 如果启用此设置，则当达到最大值时，将无法尝试创建新的识别器对象。 现有对象将继续工作。

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

若要启用错误阈值，必须在 `SpeechConfig` 对象上指定它。 创建新的识别器时，将检查此对象。 在下面的示例中，假设已创建一个名为 `config``SpeechConfig` 实例：

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
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> 此属性的默认值为 `size_t` 数据类型的特定于平台的最大值。 典型识别会消耗7到10个内部对象。

## <a name="next-steps"></a>后续步骤

* [获取语音服务试用版订阅](get-started.md)
* [了解如何使用麦克风识别语音](quickstarts/speech-to-text-from-microphone.md)