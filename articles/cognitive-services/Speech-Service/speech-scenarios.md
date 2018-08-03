---
title: Azure 认知服务语音方案 | Azure Microsoft Docs
description: 方案
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 9e6be3608f5aa5ec5d68e6bbefff6da6c23c62fd
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247948"
---
# <a name="speech-scenarios"></a>语音方案

许多方案可以使用语音技术提供支持。 我们正在分析文档中一些最常见和指出的相关功能。 对于大部分内容，[SDK](speech-sdk.md) 是启用这些方案的核心。

该页讨论如何：
> [!div class="checklist"]
> * 创建语音触发应用
> * 转录呼叫中心音频呼叫
> * 语音机器人

## <a name="voice-triggered-apps"></a>语音触发应用

许多用户想要在其应用程序上启用语音输入。 语音输入是使应用程序灵活的一种很好的方式，无论是免提使用（例如在汽车中）还是加快各种任务（例如查询新闻或天气信息的指示）。 

### <a name="voice-triggered-apps-with-baseline-models"></a>使用基线模型的语音触发应用

如果应用将在背景噪音不过大的环境中被公众使用，那么最简单快捷的方法就是下载[语音 SDK](speech-sdk.md) 并遵循相关[示例](quickstart-csharp-dotnet-windows.md)。 由 [Azure 订阅密钥](https://azure.microsoft.com/try/cognitive-services/)提供支持的 SDK 允许开发人员将音频上传到支持 Cortana 和 Skype 的基线语音识别模型。 该模型为最先进的模型，并且由上述产品使用。 可在数分钟内启动并运行。

### <a name="voice-triggered-apps-with-custom-models"></a>使用自定义模型的语音触发应用

如果应用针对特定域（例如化学、生物学或特殊饮食需求），那么可能希望考虑调整[语言模型](how-to-customize-language-model.md)。 调整语言模型将向解码器传授应用使用的最常见短语和字词。 解码器将能够使用特定域的自定义语言模型（而不是基线模型）更准确地转录语音输入。 同样，如果待使用应用的背景噪音很突出，可能需要调整声学模型。 浏览[语言适应](how-to-customize-language-model.md)和[声学适应](how-to-customize-acoustic-models.md)提供值的其他案例的文档，并访问[适应门户](https://customspeech.ai)，开始模型创建体验。 类似于基线模型，自定义模型通过[语音 SDK](speech-sdk.md) 进行呼叫，并遵循相关[示例](quickstart-csharp-dotnet-windows.md)。

## <a name="transcribe-call-center-audio-calls"></a>转录呼叫中心音频呼叫

呼叫中心会累积大量的音频。 隐藏在这些音频文件中的是可以通过听录获得的值。 可以通过获取呼叫记录发现呼叫的持续时间、情绪、客户的满意度以及呼叫提供给主叫方的常规值。

最佳起点是[批量听录 API](batch-transcription.md) 以及相关[示例](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。

将需要首先获取 [Azure 订阅密钥](https://azure.microsoft.com/try/cognitive-services/)，然后将需要查阅[文档]([Batch transcription API](batch-transcription.md))。

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>使用基线模型的转录呼叫中心音频呼叫

需要决定是否将使用内部基线模型来执行听录、调整语言或声学模型，或同时调整两者。 若要使用基线模型，API 仅需要 API 密钥。 在内部 API 将调用数据的最佳模型并调整。

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>使用自定义模型的转录呼叫中心音频呼叫

如果计划使用自定义模型，则需要该模型的 ID 以及 API 密钥。 模型 ID 可从[适应门户](https://customspeech.ai)获取。 这不是在“终结点详细信息”视图中找到的终结点 ID，而是单击该模型的“详细信息”时可检索到的模型 ID。

## <a name="voice-bots"></a>语音机器人

开发人员可以使其应用程序实现语音输出。 语音服务可以合成多种[语言](supported-languages.md)的语音，并提供用于访问该功能并将其添加到应用的[终结点](rest-apis.md)。

此外，对于想要向机器人添加更多个性和唯一性的用户，语音服务使开发人员能够自定义独特的语音字体。 类似于自定义语音识别模型，语音字体需要用户数据。 开发人员在[语音适应门户](https://customspeech.ai)中上传该数据，并开始生成独特的机器人语音品牌。 [此处](how-to-text-to-speech.md)介绍了详细信息以及[常见问题解答](faq-text-to-speech.md)页面 

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [语音 SDK 入门](speech-sdk.md)
