---
title: 使用语音服务进行语音翻译
titleSuffix: Azure Cognitive Services
description: 使用语音服务，可以将端到端、实时和多语言语音翻译添加到应用程序、工具和设备。 相同 API 可以用于语音到语音和语音到文本的转换。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 17ad32cba18915bf7f83163cd876686d42323750
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468757"
---
# <a name="what-is-speech-translation"></a>什么是语音翻译？

使用 Azure 语音服务提供的语音翻译，可以对音频流进行实时多语言语音转语音和语音转文本翻译。 使用语音 SDK，应用程序、工具和设备可以访问所提供的音频的源听录和翻译输出。 检测到语音时，会返回过渡性的听录和翻译结果，最终结果可以转换为合成语音。

Microsoft 翻译引擎有两种不同的支持方法：统计机器翻译 (SMT) 和神经机器翻译 (NMT)。 SMT 可以在给定上下文（数个单词）的情况下，使用高级统计分析来估计可能的最佳翻译。 使用 NMT 时，可以通过神经网络借助完整的语句上下文来翻译单词，这样可以提供更准确且听起来很自然的翻译。

目前，对于大多数常用语言，Microsoft 使用 NMT 进行翻译。 NMT 支持所有[可用于语音到语音转换的语言](language-support.md#speech-translation)。 语音到文本转换可能会使用 SMT 或 NMT，具体取决于语言对。 如果 NMT 支持目标语言，则 NMT 支持全译。 如果 NMT 不支持目标语言，则翻译是 NMT 和 SMT 的结合，将英语作为两种语言之间的“枢轴”。

## <a name="core-features"></a>核心功能

下面是可以通过语音 SDK 和 REST API 获得的功能：

| 使用案例 | SDK 中 IsInRole 中的声明 | REST |
|----------|-----|------|
| 包含识别结果的语音转文本翻译。 | 是 | 否 |
| 语音转语音翻译。 | 是 | 否 |
| 过渡性识别和翻译结果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>语音翻译入门

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。 下表按语言列出了语音翻译快速入门。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| [C#、.NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>代码示例

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别/翻译，以及使用自定义模型。

* [语音转文本和翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>迁移指南

如果你的应用程序、工具或产品使用[语音翻译 API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)，则可参阅我们创建的指南来迁移到语音服务。

* [从语音翻译 API 迁移到语音服务](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音到文本](rest-speech-to-text.md)
* [REST API：文本到语音转换](rest-text-to-speech.md)
* [REST API：批处理脚本和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
