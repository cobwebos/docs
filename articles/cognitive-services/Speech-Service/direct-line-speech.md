---
title: 直线语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音软件开发工具包 （SDK） 使用"直线语音"的语音助理的功能、功能和限制概述。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 714cdefa7100160a510d1922a2547ae53692d76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367799"
---
# <a name="what-is-direct-line-speech"></a>什么是 Direct Line 语音？

**直接在线语音**是一个强大的端到端解决方案，用于创建灵活、可扩展的语音助手。 它由机器人框架及其直线语音通道提供支持，该通道针对与机器人的语音转换、语音交互进行了优化。

[语音助理](voice-assistants.md)会倾听用户并采取行动进行响应，通常还会回话。 他们使用[语音到文本](speech-to-text.md)来转录用户的演讲，然后对文本的自然语言理解采取行动。 此操作通常包括使用[文本到语音](text-to-speech.md)生成的助手的语音输出。

直接在线语音为语音助理提供了最高级别的自定义和复杂。 它专为具有任务完成或命令和控制使用的开放式、自然或混合的会话方案而设计。 这种高度的灵活性具有更大的复杂性，使用自然语言输入将范围限定为定义良好的任务的方案可能需要考虑[自定义命令（预览），](custom-commands.md)以简化解决方案体验。

## <a name="getting-started-with-direct-line-speech"></a>开始使用直线语音

使用"直线语音"创建语音助手的第一个步骤是[获取语音订阅密钥](get-started.md)、创建与该订阅关联的新自动程序以及将自动程序连接到"直线语音"通道。

   ![直线语音编排服务流的概念图](media/voice-assistants/overview-directlinespeech.png "语音通道流")

有关使用"直线语音"创建简单语音助手的完整分步指南，请参阅[使用语音 SDK 和直线语音通道启用语音的机器人教程](tutorial-voice-enable-your-bot-speech-sdk.md)。

我们还提供快速入门，旨在让您快速运行代码和学习 API。 此表包括按语言和平台组织的语音助理快速入门的列表。

| 快速入门 | Platform | API 参考 |
|------------|----------|---------------|
| C#、UWP | Windows | [浏览](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [浏览](https://aka.ms/csspeech/javaref) |
| Java | Android | [浏览](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>示例代码

在 GitHub 上提供了用于创建语音助理的示例代码。 这些示例涵盖客户端应用程序，用于以几种流行的编程语言连接到您的助手。

* [语音助手示例 (SDK)](https://aka.ms/csspeech/samples)
* [教程：语音启用您的助手与语音 SDK， C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自定义

使用语音服务构建的语音助手可以使用用于[语音到文本](speech-to-text.md)、[文本到语音](text-to-speech.md)和[自定义关键字选择](speech-devices-sdk-create-kws.md)的完整自定义选项。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](supported-languages.md)）。

直接线路语音及其语音助理的相关功能是[虚拟助理解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的理想补充。 尽管 Direct Line 语音可以与任何兼容的机器人配合使用，但这些资源为高质量的对话体验提供了可重用的基线，以及快速入门的常见支持技能和模型。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
* [创建和部署基本机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [获取虚拟助理解决方案和企业模板](https://github.com/Microsoft/AI)
