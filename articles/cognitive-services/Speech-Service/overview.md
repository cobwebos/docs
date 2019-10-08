---
title: 什么是语音服务？
titleSuffix: Azure Cognitive Services
description: 语音服务在单个 Azure 订阅中统合了语音转文本、文本转语音以及语音翻译功能。 使用语音 SDK、语音设备 SDK 或 REST API 可以轻松在应用程序、工具和设备中添加语音。 可将语音功能添加到现有的聊天机器人，在翻译应用程序中将文本转换为语音，或者听录大量的呼叫中心数据。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 993064a36bd12c95e83ffb7c9635c3e4dfcec0f0
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803343"
---
# <a name="what-are-the-speech-services"></a>什么是语音服务？

语音服务在单个 Azure 订阅中统合了语音转文本、文本转语音以及语音翻译功能。 使用[语音 SDK](speech-sdk-reference.md)、[语音设备 SDK](https://aka.ms/sdsdk-quickstart) 或 [REST API](rest-apis.md) 可以轻松在应用程序、工具和设备中启用语音。

> [!IMPORTANT]
> 语音服务已替代必应语音 API、语音翻译和自定义语音。 有关迁移说明，请参阅*操作指南 > 迁移*。

这些功能构成了 Azure 语音服务。 请使用下表中的链接详细了解每项功能的常见用例或浏览 API 参考信息。

| 服务 | Feature | 说明 | SDK 中 IsInRole 中的声明 | REST |
|---------|---------|-------------|-----|------|
| [语音转文本](speech-to-text.md) | 语音转文本 | 语音转文本可将音频流实时听录为应用程序、工具或设备可以使用或显示的文本。 结合[语言理解 (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) 使用语音转文本可以从听录的语音中派生用户意向，以及处理语音命令。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [批量听录](batch-transcription.md) | 使用批量听录能够以异步方式对大量的数据进行语音转文本听录。 这是一个基于 REST 的服务，它使用的终结点与自定义和模型管理相同。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| | [对话听录](conversation-transcription-service.md) | 启用实时语音识别、说话人识别和分割聚类。 它非常适合用于听录能够区分说话人的面对面会谈场景。 | 是 | 否 |
| | [创建自定义语音模型](#customize-your-speech-experience) | 如果使用语音转文本在独特的环境中进行识别和听录，则可以创建并训练自定义的声学、语言和发音模型，以解决环境干扰或行业特定的词汇。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| [文本转语音](text-to-speech.md) | 文本转语音 | 文本转语音可使用[语音合成标记语言 (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml) 将输入文本转换为类似人类的合成语音。 可以选择标准语音或神经语音（请参阅[语言支持](language-support.md)）。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [创建自定义语音](#customize-your-speech-experience) | 创建专属于品牌或产品的自定义语音字体。 | 否 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [语音翻译](speech-translation.md) | 语音翻译 | 使用语音翻译可在应用程序、工具和设备中实现实时的多语言语音翻译。 进行语音转语音和语音转文本翻译时可以使用此服务。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | 否 |
| [语音优先虚拟助手](voice-first-virtual-assistants.md) | 语音优先虚拟助手 | 自定义虚拟助手使用 Azure 语音服务为开发人员助力，使开发人员可以为其应用程序和体验创建自然的、类似于人类的对话接口。 Bot Framework 的 Direct Line Speech 通道通过为兼容机器人提供协调的、安排好的入口点来实现延迟时间短、可靠性高的双向语音交互，从而增强了这些功能。 | [是](voice-first-virtual-assistants.md) | 否 |

## <a name="news-and-updates"></a>新增功能和更新

了解 Azure 语音服务的新增功能。

* 2019 年 9 月
  * 发布了语音 SDK 1.7.0。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。
* 2019 年 8 月
  * **新教程**：[使用语音 SDK、C# 为机器人启用语音](tutorial-voice-enable-your-bot-speech-sdk.md)
  * 添加了一种新的说话风格 [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)，用于 `en-US-JessaNeural` 语音。 
* 2019 年 7 月
  * 发布了语音 SDK 1.6.0。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。
* 2019 年 5 月 - [会话听录](conversation-transcription-service.md)、[呼叫中心听录](call-center-transcription.md)和[语音优先虚拟助手](voice-first-virtual-assistants.md)的文档现已提供。
* 2019 年 5 月
  * 发布了语音 SDK 1.5.1。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。
  * 发布了语音 SDK 1.5.0。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。

## <a name="try-speech-services"></a>试用语音服务

我们提供了适用于大多数流行编程语言的快速入门，旨在帮助你在 10 分钟以内运行代码。 下表包含有关每项功能在最流行编程语言中的用法的快速入门。 使用左侧的导航栏可以浏览其他语言和平台。

| 语音转文本 (SDK) | 文本转语音 (SDK) | 翻译 (SDK) |
|----------------------|----------------------|-------------------|
| [C#、.NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#、.NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java（Windows、Linux）](quickstart-translate-speech-java-jre.md) |
| [JavaScript（浏览器）](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#、.NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python（Windows、Linux、macOS）](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#、.NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java（Windows、Linux）](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> “语音转文本”和“文本转语音”功能也有 REST 终结点和相关联的快速入门。

有机会使用语音服务后，请尝试学习有关如何使用语音 SDK 和 LUIS 从语音中识别意向的教程。

* [教程：使用适用于 C# 的语音 SDK 和 LUIS 从语音中识别意向](how-to-recognize-intents-from-speech-csharp.md)
* [教程：使用语音 SDK、C# 为机器人启用语音](tutorial-voice-enable-your-bot-speech-sdk.md)
* [教程：生成 Flask 应用以翻译文本、分析情绪以及将翻译后的文本合成为语音 - REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>获取示例代码

GitHub 中提供了每个 Azure 语音服务的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。 使用以下链接查看 SDK 和 REST 示例：

* [语音转文本、文本转语音和语音翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [语音优先虚拟助手示例 (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>自定义语音体验

Azure 语音服务能够很好地与内置模型配合工作，但是，你可能想要根据自己的产品或环境，进一步自定义和优化体验。 自定义选项的范围从声学模型优化，到专属于自有品牌的语音字体。 生成自定义模型后，可将其与任何 Azure 语音服务配合使用。

| 语音服务 | 平台 | 说明 |
|----------------|-------------|-------------|
| 语音转文本 | [自定义语音](https://aka.ms/customspeech) | 根据需要和可用数据自定义语音识别模型。 克服语音识别障碍，如说话风格、词汇和背景噪音。 |
| 文本转语音 | [自定义语音](https://aka.ms/customvoice) | 使用可用语音数据为文本转语音应用生成可识别的独一无二的语音。 可以通过调整一组语音参数来进一步微调语音输出。 |

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [免费获取语音服务订阅密钥](get-started.md)
