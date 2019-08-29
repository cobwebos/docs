---
title: 语音到文本语音服务
titleSuffix: Azure Cognitive Services
description: Speech Services 中的语音到文本功能 (也称为语音到文本) 使实时传输音频流成为文本, 应用程序、工具或设备可以使用、显示该文本, 并作为命令输入执行操作。 此服务由 Microsoft 为 Cortana 和 Office 产品使用的同一识别技术提供支持, 并与翻译和文本到语音功能无缝协作。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fa80ebea93ae897ba01ff54bdb797ed4194665cd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068865"
---
# <a name="what-is-speech-to-text"></a>什么是语音到文本？

来自 Azure 语音服务的语音到文本 (也称为语音到文本) 使实时传输音频流成为文本, 应用程序、工具或设备可以使用、显示该文本, 并作为命令输入执行操作。 此服务由 Microsoft 为 Cortana 和 Office 产品使用的同一识别技术提供支持, 并与翻译和文本到语音功能无缝协作。  有关可用的语音到文本语言的完整列表, 请参阅[支持的语言](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)。

默认情况下, 语音到文本服务使用通用语言模型。 此模型是使用 Microsoft 拥有的数据训练的, 部署在云中。 这对于对话和听写方案是最佳的。 如果使用语音转文本在独特的环境中进行识别和听录，则可以创建并训练自定义的声学、语言和发音模型，以解决环境干扰或行业特定的词汇。

你可以轻松地从麦克风捕获音频, 从流中读取, 或使用语音 SDK 和 REST Api 从存储访问音频文件。 语音 SDK 支持 WAV/PCM 16 位、16 kHz/8 kHz、用于语音识别的单声道音频。 使用[语音到文本 REST 终结点](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)或[批处理服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)时, 支持其他音频格式。

## <a name="core-features"></a>核心功能

以下是通过语音 SDK 和 REST Api 提供的功能:

| 使用案例 | SDK | REST |
|----------|-----|------|
| 转录 short 最谈话 (< 15 秒)。 仅支持最终的脚本结果。 | 是 | 是 |
| 持续处理长最谈话和流式传输音频 (> 15 秒)。 支持临时和最终的脚本结果。 | 是 | 否 |
| 通过[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)从识别结果派生。 | 是 | 否\* |
| 异步批处理音频文件。 | 否 | 是\** |
| 创建和管理语音模型。 | 否 | 是\** |
| 创建和管理自定义模型部署。 | 否 | 是\** |
| 创建准确性测试来度量基线模型与自定义模型的准确性。 | 否 | 是\** |
| 管理订阅。 | 否 | 是\** |

\* *可以使用单独的 LUIS 订阅获取 LUIS 意向和实体。对于此订阅, SDK 可以为你调用 LUIS, 并提供实体和意向结果。使用 REST API，可以自己调用 LUIS 以使用 LUIS 订阅获取意向和实体。*

\** *使用 cris.ai 终结点可以使用这些服务。请参阅[Swagger 引用](https://westus.cris.ai/swagger/ui/index)。*

## <a name="get-started-with-speech-to-text"></a>开始语音到文本

我们提供了适用于大多数流行编程语言的快速入门，旨在帮助你在 10 分钟以内运行代码。 [此表](https://aka.ms/csspeech#5-minute-quickstarts)包含按照 platfrom.details.heap.alignedallocate 和语言组织的语音 SDK 快速入门的完整列表。  还可以在[此处](https://aka.ms/csspeech#reference)找到 API 引用。

如果希望使用语音到文本 REST 服务, 请参阅[REST api](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)。

## <a name="tutorials-and-sample-code"></a>教程和示例代码

有机会使用语音服务后，请尝试学习有关如何使用语音 SDK 和 LUIS 从语音中识别意向的教程。

* [教程：使用适用于 C# 的语音 SDK 和 LUIS 从语音中识别意向](how-to-recognize-intents-from-speech-csharp.md)

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。

* [语音到文本示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自定义

除了语音服务使用的标准基线模型外, 还可以根据需要使用可用数据自定义模型, 以克服语音识别障碍, 如说话风格、词汇和背景噪音, 请参阅[自定义语音](how-to-custom-speech.md)

> [!NOTE]
> 自定义选项因语言/区域设置而异 (请参阅[支持的语言](supported-languages.md))。

## <a name="migration-guides"></a>迁移指南

> [!WARNING]
> 将于2019年10月15日停用必应语音。

如果你的应用程序、工具或产品正在使用必应语音 Api 或自定义语音, 我们已创建了可帮助你迁移到语音服务的指南。

* [从必应语音迁移到语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [从自定义语音迁移到语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](https://aka.ms/csspeech)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
