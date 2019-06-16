---
title: 使用 Azure 语音服务的语音翻译
titlesuffix: Azure Cognitive Services
description: 语音服务让你的语音的端到端、 实时、 多语言翻译添加到应用程序、 工具和设备。 相同 API 可以用于语音到语音和语音到文本的转换。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 84d212129c5225fd0efebfca5640cfc3d32e8a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072397"
---
# <a name="what-is-speech-translation"></a>语音翻译是什么？

语音翻译，Azure 语音服务，从启用音频流的实时、 多语言语音语音和语音到文本的翻译。 使用语音 SDK 中，应用程序、 工具和设备有权访问源转录和翻译输出提供音频。 检测到语音，并可将总决赛结果转换为合成语音返回临时转录和翻译结果。

Microsoft 的转换引擎由两个不同的方法： 统计机器翻译 (SMT) 和神经网络机器翻译 (NMT)。 SMT 使用高级统计分析来估计给出几个单词的上下文的最佳可能翻译。 与 NMT，神经网络用于通过使用句子的完整上下文将单词提供更准确、 自然发音的翻译。

当前，Microsoft 使用 NMT 用于翻译为最常用的语言。 NMT 支持所有[可用于语音到语音转换的语言](language-support.md#speech-translation)。 语音到文本转换可能会使用 SMT 或 NMT，具体取决于语言对。 NMT 支持目标语言，则完整翻译时，NMT 提供支持。 NMT 不支持目标语言，转换时，混合 NMT 和 SMT，这两种语言之间使用英语为"透视"。

## <a name="core-features"></a>核心功能

此处是通过 Speech SDK 和 REST Api 提供的功能：

| 使用案例 | SDK 中 IsInRole 中的声明 | REST |
|----------|-----|------|
| 语音到文本与识别结果的转换。 | 是 | 否 |
| 语音到语音翻译。 | 是 | 否 |
| 识别和转换的中间结果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>开始使用语音翻译

我们提供了快速入门旨在让您在 10 分钟内运行代码。 此表包含一系列按语言的语音翻译快速入门。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>代码示例

Speech SDK 的示例代码位于 GitHub 上提供。 这些示例涵盖了从文件或流，连续和单步识别/转换读取音频和使用自定义模型等常见方案。

* [语音到文本和转换示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>迁移指南

> [!WARNING]
> 语音翻译将在 2019 年 10 月 15 日停用。

如果应用程序、 工具或产品使用的语音翻译，我们创建了指南，以帮助您迁移到语音服务。

* [将语音翻译 API 迁移到语音服务](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
