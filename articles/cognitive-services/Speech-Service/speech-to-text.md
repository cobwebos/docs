---
title: 语音转文本 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音转文本功能，可将音频流实时听录为文本。 应用程序、工具或设备可以使用、显示和处理此文本输入。 此服务可与文本转语音（语音合成）和语音翻译功能无缝协作。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 90a5ec8c58865c3aa1b210db51b9ffeb7169d641
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640406"
---
# <a name="what-is-speech-to-text"></a>什么是语音转文本？

使用语音服务提供的语音转文本（也称为语音识别）功能，可将音频流实时听录为文本。 应用程序、工具或设备可以使用、显示和处理此文本即命令输入。 此服务由 Microsoft 对 Cortana 和 Office 产品使用的同一识别技术提供支持。 它可与<a href="./speech-translation.md" target="_blank">翻译<span class="docon docon-navigate-external x-hidden-focus"></span></a>和<a href="./text-to-speech.md" target="_blank">文本转语音<span class="docon docon-navigate-external x-hidden-focus"></span></a>服务产品无缝地协同工作。 有关可用语音转文本语言的完整列表，请参阅[支持的语言](language-support.md#speech-to-text)。

语音转文本服务默认使用通用语言模型。 此模型已使用 Microsoft 自有的数据训练，部署在云中。 它非常适合用于对话和听写方案。 使用语音转文本在独特的环境中进行识别和听录时，可以创建并训练自定义的声学、语言和发音模型。 自定义有助于解决环境干扰或特定于行业的词汇的问题。

通过使用其他参考文本作为输入，语音转文本服务还支持[发音评估](rest-speech-to-text.md#pronunciation-assessment-parameters)功能（用于评估语音发音），并向说话人提供有关语音准确性和流畅度的反馈。 通过发音评估，语言学习者可以练习、获得即时反馈并改进其发音，因此能够自信地讲话和演示。 教师可以使用此功能来实时评估多个说话人的发音。 此功能目前支持美国英语，并与专家进行的语音评估高度相关。

> [!NOTE]
> 必应语音于2019年10月15日停用。 如果你的应用程序、工具或产品正在使用必应语音 Api，我们已创建了可帮助你迁移到语音服务的指南。
> - [从必应语音迁移到语音服务](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started-with-speech-to-text"></a>语音转文本入门

语音转文本服务通过[语音 SDK](speech-sdk.md) 提供。 有几种常见方案可作为快速入门，以各种语言和平台提供：

 - [快速入门：识别来自麦克风输入的语音](quickstarts/speech-to-text-from-microphone.md)
 - [快速入门：从文件中识别语音](quickstarts/speech-to-text-from-file.md)
 - [快速入门：识别存储在 Blob 存储中的语音](quickstarts/from-blob.md)

如果你偏向于使用语音转文本 REST 服务，请参阅 [REST API](rest-speech-to-text.md)。

 - [快速入门：通过参考输入进行发音评估](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>教程和示例代码

有机会使用语音服务后，请尝试学习有关如何使用语音 SDK 和 LUIS 从语音中识别意向的教程。

- [教程：使用 C# 通过语音 SDK 和 LUIS 从语音中识别意向](how-to-recognize-intents-from-speech-csharp.md)

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。

- [语音转文本示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [发音评估示例 (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>自定义

除了标准语音服务模型外，还可以创建自定义模型。 自定义有助于克服语音识别障碍，如说话风格、词汇和背景噪音，详见[自定义语音识别](how-to-custom-speech.md)。 自定义选项因语言/区域设置而异，请参阅[支持的语言](supported-languages.md)以验证相关支持。

## <a name="batch-transcription"></a>批量听录

批处理脚本是一组 REST API 操作，使你能够在存储中转录大量音频。 你可以指向具有共享访问签名 (SAS) URI 的音频文件并异步接收听录结果。 有关如何使用批处理脚本 API 的详细信息，请参阅操作 [方法](batch-transcription.md) 。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>后续步骤

- [免费获取语音服务订阅密钥](get-started.md)
- [获取语音 SDK](speech-sdk.md)
