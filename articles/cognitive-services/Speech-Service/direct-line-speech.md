---
title: 直拨语音语音服务
titleSuffix: Azure Cognitive Services
description: 概述了语音助手使用语音软件开发工具包（SDK）的直接线路语音功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 72029d1d56c9d561e629cc750244fd7de8ec113a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507172"
---
# <a name="about-direct-line-speech"></a>关于直接行语音

[语音助手](voice-assistants.md)会倾听用户的响应，并在响应时执行操作，这通常是一回谈话。 他们使用[语音到文本](speech-to-text.md)转录用户的语音，然后对文本的自然语言理解采取措施。 此操作通常包含用[文本到语音功能](text-to-speech.md)生成的助手的语音输出。 设备通过语音 SDK 的 `DialogServiceConnector` 对象连接到助手。

**直接线路语音**是一种可靠的端到端解决方案，可用于创建灵活、可扩展的语音助手，并通过机器人框架和其直接线路语音通道提供支持，这些解决方案经过优化，可用于与机器人的语音语音交互。

直接连线语音提供语音助手的最高级别的自定义和复杂程度。 它非常适合用于与任务完成或命令和控制使用一起处于开放、自然或混合状态的会话方案。 这种高度的灵活性带来了更大的复杂性，并且通过自然语言输入以定义完善的任务为作用域的方案，可能需要考虑使用[自定义命令（预览版）](custom-commands.md)来简化解决方案的体验。

## <a name="getting-started-with-direct-line-speech"></a>直接行语音入门

使用 "直接线路语音" 创建语音助手的第一步是[获取语音订阅密钥](get-started.md)，创建与该订阅关联的新机器人，并将机器人连接到直接线路语音通道。

   ![直接连线语音业务流程服务流的概念图](media/voice-assistants/overview-directlinespeech.png "语音通道流")

有关使用直行语音创建简单语音助手的完整循序渐进指南，请参阅[语音教程教程，了解如何使用语音 SDK 和直接线路语音通道实现机器人](tutorial-voice-enable-your-bot-speech-sdk.md)。

我们还提供了一些快速入门，旨在让你在不到10分钟的时间内运行代码。 此表包含按语言组织的语音助手快速入门列表。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| C#、UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>代码示例

GitHub 上提供了用于创建语音助手的示例代码。 这些示例涵盖了用几种常用编程语言连接到助手的客户端应用程序。

* [语音助手示例（SDK）](https://aka.ms/csspeech/samples)
* [教程：通过语音 SDK 使用语音启用助手，C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自定义

使用 Azure 语音服务构建的语音助手可以使用各种自定义选项，这些选项可用于[语音到文本](speech-to-text.md)、[文本到语音](text-to-speech.md)和[自定义关键字选择](speech-devices-sdk-create-kws.md)。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](supported-languages.md)）。

"语音助手" 的 "直接行语音" 及其相关功能是 "[虚拟助手解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)" 的理想补充。 尽管直接行语音可以与任何兼容机器人配合使用，但这些资源为高质量的对话体验提供了可重用的基线，并为快速入门提供了常见的支持技能和模型。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
* [创建和部署基本机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [获取虚拟助手解决方案和企业模板](https://github.com/Microsoft/AI)
