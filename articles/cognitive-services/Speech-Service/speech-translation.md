---
title: 语音翻译概述-语音服务
titleSuffix: Azure Cognitive Services
description: 语音翻译允许您将语音的端到端实时、多语言翻译添加到您的应用程序、工具和设备。 相同 API 可以用于语音到语音和语音到文本的转换。 本文概述了语音翻译服务的优势和功能。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: 语音翻译
ms.openlocfilehash: e4bc2f07fce3917bfa59f79305498bb91889344d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359794"
---
# <a name="what-is-speech-translation"></a>什么是语音翻译？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

在本概述中，你将了解语音翻译服务的优点和功能，它允许音频流的实时、多语言语音到语音和语音到文本转换。 使用语音 SDK，应用程序、工具和设备可以访问所提供的音频的源听录和翻译输出。 检测到语音时，会返回过渡脚本和翻译结果，最终结果可以转换为合成语音。

Microsoft 翻译引擎有两种不同的支持方法：统计机器翻译 (SMT) 和神经机器翻译 (NMT)。 SMT 可以在给定上下文（数个单词）的情况下，使用高级统计分析来估计可能的最佳翻译。 使用 NMT 时，可以通过神经网络借助完整的语句上下文来翻译单词，这样可以提供更准确且听起来很自然的翻译。

目前，对于大多数常用语言，Microsoft 使用 NMT 进行翻译。 NMT 支持所有[可用于语音到语音转换的语言](language-support.md#speech-translation)。 语音到文本转换可能会使用 SMT 或 NMT，具体取决于语言对。 如果 NMT 支持目标语言，则 NMT 支持全译。 如果 NMT 不支持目标语言，则翻译是 NMT 和 SMT 的结合，将英语作为两种语言之间的“枢轴”。

## <a name="core-features"></a>核心功能

* 包含识别结果的语音转文本翻译。
* 语音转语音翻译。
* 支持翻译为多种目标语言。
* 过渡性识别和翻译结果。

## <a name="get-started"></a>入门 

请参阅[快速入门](get-started-speech-translation.md)以开始使用语音翻译。 语音翻译服务通过[语音 SDK](speech-sdk.md) 和[语音 CLI](spx-overview.md) 提供。

## <a name="sample-code"></a>代码示例

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别/翻译，以及使用自定义模型。

* [语音转文本和翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>迁移指南

如果你的应用程序、工具或产品正在使用 [语音翻译 API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)，我们已创建了可帮助你迁移到语音服务的指南。

* [从语音翻译 API 迁移到语音服务](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* 完成语音翻译[快速入门](get-started-speech-translation.md)
* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
