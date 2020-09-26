---
title: 文本到语音概述-语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务中的文本转语音功能可让应用程序、工具或设备将文本转换为类似于人类的自然合成语音。 本文概述了文本到语音服务的优势和功能。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: 文本转语音
ms.openlocfilehash: 5d60279a2e3cb6aa7226f518783d53a1a38ddaa8
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91357448"
---
# <a name="what-is-text-to-speech"></a>什么是文本转语音？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

在本概述中，你将了解文本到语音服务的优点和功能，使你的应用程序、工具或设备可以将文本转换为类似于用户的合成语音。 选择标准和神经声音，或创建产品或品牌的独特的自定义声音。 75多个标准语音在45多种语言和区域设置中提供，5个神经声音在选择的语言和区域设置中可用。 有关支持的语音、语言和区域设置的完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

> [!NOTE]
> 必应语音于2019年10月15日停用。 如果你的应用程序、工具或产品正在使用必应语音 Api 或自定义语音，我们已创建了可帮助你迁移到语音服务的指南。
> - [从必应语音迁移到语音服务](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>核心功能

* 语音合成-使用 [语音 SDK](quickstarts/text-to-speech-audio-file.md) 或 [REST API](rest-text-to-speech.md) 通过标准、神经或自定义语音转换文本到语音转换。

* 长音频的异步合成 - 使用[长音频 API](long-audio-api.md) 异步合成 10 分钟以上的文本转语音文件（例如有声书籍或讲座）。 不同于使用语音 SDK 或语音转文本 REST API 执行的合成，响应不会实时返回。 预期会异步发送请求，以轮询的方式获取响应，并会下载合成音频（在服务提供该音频的情况下）。 仅支持自定义的神经声音。

* 标准语音 - 使用统计参数合成和/或串联合成技术创建。 这些语音的辨识度很高，且听起来非常自然。 你可以轻松地让应用程序使用多种语音选项以 45 种以上的语言讲述。 这些声音提供较高的发音准确度，支持缩写、缩略词扩展、日期/时间解释、多音字等。 有关标准语音的完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

* 神经语音 - 深层神经网络用于克服有关口语中的重读和语调的传统语音合成限制。 韵律预测和语音合成以同步方式执行，使输出听起来更流畅且自然。 使用神经语音可使得与聊天机器人和语音助手的交流更加自然且富有吸引力、将数字文本（如电子书）转换为有声读物以及增强车载导航系统。 神经语音可以生成类人的自然韵律和清晰的字词发音，当你在与 AI 系统交互时，它可以显著减轻听力疲劳。 有关神经语音的完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

* 语音合成标记语言 (SSML) - 一种基于 XML 的标记语言，用于自定义语音转文本输出。 使用 SSML，你可以调整音调、添加暂停、改进发音、提高或降低语速、增加或减少音量，以及将多个语音赋予单个文档。 请参阅 [SSML](speech-synthesis-markup.md)。

## <a name="get-started"></a>入门

请参阅[快速入门](get-started-text-to-speech.md)以开始使用文本转语音。 文本转语音服务通过[语音 SDK](speech-sdk.md)、[REST API](rest-text-to-speech.md) 和[语音 CLI](spx-overview.md) 提供

## <a name="sample-code"></a>代码示例

GitHub 上提供了文本转语音的示例代码。 这些示例涵盖了最流行编程语言的文本转语音转换。

- [文本转语音示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>自定义

除了标准和神经声音外，还可以创建和微调产品或品牌独有的自定义语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 有关详细信息，请参阅 [自定义语音入门](how-to-custom-voice.md)

## <a name="pricing-note"></a>定价说明

使用文本转语音服务时，需按照转换为语音的每个字符（包括标点）付费。 尽管 SSML 文档本身不计费，但用于调整文本转语音方式的可选元素（例如音素和音节）将算作计费字符。 下面列出了计费的内容：

- 在请求的 SSML 正文中传递给文本转语音服务的文本
- 请求正文的文本字段中所有 SSML 格式的标记，`<speak>` 和 `<voice>` 标记除外
- 字母、标点、空格、制表符、标记和所有空白字符
- Unicode 中定义的每个码位

有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每个中文、日语和韩语字符算作两个计费字符。

## <a name="reference-docs"></a>参考文档

- [语音 SDK](speech-sdk.md)
- [REST API：文本转语音](rest-text-to-speech.md)

## <a name="next-steps"></a>后续步骤

- [获取免费语音服务订阅](overview.md#try-the-speech-service-for-free)
- [获取语音 SDK](speech-sdk.md)
