---
title: 语音到文本与 Azure 的语音服务
titleSuffix: Azure Cognitive Services
description: 从 Azure 语音服务，也称为语音到文本，使实时听录的音频流到应用程序、 工具或设备便可以使用，文本的语音到文本显示，并作为命令的输入对其执行操作。 此服务是由相同的识别技术，Microsoft 使用 Cortana 和 Office 产品，并无缝配合，翻译和文本到语音转换提供支持。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7aa4492a22c8aca1e54570adb2811e4cb13caf7b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900771"
---
# <a name="what-is-speech-to-text"></a>什么是语音到文本？

从 Azure 语音服务，也称为语音到文本，使实时听录的音频流到应用程序、 工具或设备便可以使用，文本的语音到文本显示，并作为命令的输入对其执行操作。 此服务是由相同的识别技术，Microsoft 使用 Cortana 和 Office 产品，并无缝配合，翻译和文本到语音转换提供支持。

默认情况下，语音转文本服务使用的通用语言模型。 此模型使用 Microsoft 拥有的数据训练和已部署的云。 其最适合于交谈和听写方案。 如果将语音转文本用于识别和听录独特环境中的，可以创建并定型到地址避免环境噪音或特定于行业的词汇的自定义声学、 语言和发音模型。

可以轻松地捕获来自麦克风的音频、 从流中读取或从存储使用语音 SDK 和 REST Api 访问音频文件。 Speech SDK 支持语音识别的 WAV/PCM 16 位、 16 kHz、 单声道音频。 使用支持其他音频格式[语音到文本 REST 终结点](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-formats)或[批处理脚本服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)。

## <a name="core-features"></a>核心功能

此处是通过 Speech SDK 和 REST Api 提供的功能：

| 使用案例 | SDK 中 IsInRole 中的声明 | REST |
|----------|-----|------|
| 转录短的查询文本 (< 15 秒)。 仅支持最终脚本结果。 | 是 | 是 |
| 连续的长语音样本和流式处理音频转录 (> 15 秒)。 支持中期和最终脚本结果。 | 是 | 否 |
| 从与识别结果派生意向[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)。 | 是 | 否\* |
| 以异步方式执行批处理的音频文件的脚本。 | 否 | 是\** |
| 创建和管理语音模型。 | 否 | 是\** |
| 创建和管理自定义模型部署。 | 否 | 是\** |
| 创建准确性测试以衡量与自定义模型的基线模型的准确性。 | 否 | 是\** |
| 管理订阅。 | 否 | 是\** |

\* *可以使用单独的 LUIS 订阅获取 LUIS 意向和实体。与此订阅，SDK，可以为您调用 LUIS 并提供实体和意向的结果。使用 REST API，可以自己调用 LUIS 以使用 LUIS 订阅获取意向和实体。*

\** *这些服务可使用 cris.ai 终结点。请参阅[Swagger 引用](https://westus.cris.ai/swagger/ui/index)。*

## <a name="get-started-with-speech-to-text"></a>开始使用语音转文本

我们提供了最常用编程语言中的每个旨在让您在 10 分钟内运行代码的快速入门。 此表包括按语言的语音 SDK 快速入门的完整列表。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| [Javascript 中，浏览器](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser、Windows、Linux、macOS | [Browse](https://aka.ms/AA434tv) |
| [Javascript Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows、Linux、macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows、Linux、macOS | [Browse](https://aka.ms/AA434tr)  |

如果你愿意使用语音到文本 REST 服务，请参阅[REST Api](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)。

## <a name="tutorials-and-sample-code"></a>教程和示例代码

在过去的人可以使用语音服务后，请尝试我们本教程教您如何识别从语音使用语音 SDK 和 LUIS 意向。

* [教程：从使用语音 SDK 和 LUIS，语音意图识别C#](how-to-recognize-intents-from-speech-csharp.md)

Speech SDK 的示例代码位于 GitHub 上提供。 这些示例涵盖了从文件或流，连续和单步识别读取音频和使用自定义模型等常见方案。

* [语音到文本示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [批处理脚本示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自定义

除了使用语音服务的通用模型，您可以创建自定义声学、 语言和发音模型特定到您的体验。 下面是自定义选项的列表：

| 模型 | 描述 |
|-------|-------------|
| [声学模型](how-to-customize-acoustic-models.md) | 创建自定义声学模型将应用程序、 工具或设备使用在特定环境中，如汽车或特定录制条件与工厂中的情况下十分有用。 示例包括带有口音的讲话、特定的背景噪音，或使用特定的麦克风录制音频。 |
| [语言模型](how-to-customize-language-model.md) | 创建自定义语言模型，以提高脚本的特定于行业的词汇和语法，如医疗术语中或 IT 专业术语。 |
| [发音模型](how-to-customize-pronunciation.md) | 使用自定义发音模型中，您可以定义的拼音窗体和显示字词或术语。 它适用于处理自定义术语，如产品名称或首字母缩略词。 只需使用发音文件（简单的 .txt 文件）即可。 |

> [!NOTE]
> 自定义选项因语言/区域设置而异 (请参阅[支持的语言](supported-languages.md))。

## <a name="migration-guides"></a>迁移指南

> [!WARNING]
> 必应语音将在 2019 年 10 月 15 日停用。

如果应用程序、 工具或产品使用必应语音 Api 或自定义语音，我们创建了指南，以帮助您迁移到语音服务。

* [从必应语音将迁移到语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [将自定义语音迁移到语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音到文本](rest-speech-to-text.md)
* [REST API：Text-to-speech](rest-text-to-speech.md)
* [REST API：批处理脚本和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
