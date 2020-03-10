---
title: 语音到文本语音服务
titleSuffix: Azure Cognitive Services
description: 通过语音到文本功能，可以将音频流实时传输到文本中。 应用程序、工具或设备可以对此文本输入使用、显示和执行操作。 此服务可与文本到语音（语音合成）和语音翻译功能无缝协作。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: erhopf
ms.openlocfilehash: 8518f92a4f4df1686d4b338783a93d969e04d219
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388303"
---
# <a name="what-is-speech-to-text"></a>什么是语音转文本？

来自语音服务的语音到文本（也称为语音识别）允许实时将音频流传输到文本中。 应用程序、工具或设备可以使用、显示此文本并对其执行操作，作为命令输入。 此服务由 Microsoft 为 Cortana 和 Office 产品使用的同一识别技术提供支持。 它可与<a href="./speech-translation.md" target="_blank">翻译<span class="docon docon-navigate-external x-hidden-focus"></span> </a>和<a href="./text-to-speech.md" target="_blank">文本到语音<span class="docon docon-navigate-external x-hidden-focus"></span> </a>服务产品无缝配合使用。 有关可用的语音到文本语言的完整列表，请参阅[支持的语言](language-support.md#speech-to-text)。

语音到文本服务默认使用通用语言模型。 此模型是使用 Microsoft 拥有的数据训练的，部署在云中。 这对于对话和听写方案是最佳的。 使用语音到文本进行识别和在独特的环境中工作时，可以创建自定义声音、语言和发音模型并对其进行训练。 自定义有助于解决环境干扰或特定于行业的词汇。

> [!NOTE]
> 必应语音于2019年10月15日停用。 如果你的应用程序、工具或产品正在使用必应语音 Api 或自定义语音，我们已创建了可帮助你迁移到语音服务的指南。
> - [从必应语音迁移到语音服务](how-to-migrate-from-bing-speech.md)
> - [从自定义语音迁移到语音服务](how-to-migrate-from-custom-speech-service.md)

## <a name="get-started-with-speech-to-text"></a>开始语音到文本

语音到文本服务可通过[语音 SDK](speech-sdk.md)获得。 在各种语言和平台中，有几种常见方案可用于快速入门：

 - [快速入门：通过麦克风输入识别语音](quickstarts/speech-to-text-from-microphone.md)
 - [快速入门：从文件识别语音](quickstarts/speech-to-text-from-file.md)
 - [快速入门：识别存储在 blob 存储中的语音](quickstarts/from-blob.md)

如果希望使用语音到文本 REST 服务，请参阅[REST api](rest-speech-to-text.md)。

## <a name="tutorials-and-sample-code"></a>教程和示例代码

有机会使用语音服务后，请尝试学习有关如何使用语音 SDK 和 LUIS 从语音中识别意向的教程。

- [教程：使用 Speech SDK 和 LUIS 从语音识别意向，使用C#](how-to-recognize-intents-from-speech-csharp.md)

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。

- [语音到文本示例（SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自定义

除了标准语音服务模型外，还可以创建自定义模型。 自定义有助于克服语音识别障碍，如说话风格、词汇和背景噪音，请参阅[自定义语音](how-to-custom-speech.md)。 自定义选项因语言/区域设置而异，请参阅[支持的语言](supported-languages.md)来验证支持。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>后续步骤

- [免费获取语音服务订阅密钥](get-started.md)
- [获取语音 SDK](speech-sdk.md)
