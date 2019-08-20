---
title: 自定义语音首次虚拟助手 (预览版)-语音服务
titleSuffix: Azure Cognitive Services
description: 概述在机器人框架和认知服务语音软件开发工具包 (SDK) 上使用直接线路语音通道的自定义语音首次虚拟助手的功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967594"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>关于自定义语音首次虚拟助手预览版

自定义虚拟助手使用 Azure 语音服务为开发人员助力，使开发人员可以为其应用程序和体验创建自然的、类似于人类的对话接口。 Bot Framework 的 Direct Line Speech 通道通过为兼容机器人提供协调的、安排好的入口点来实现延迟时间短、可靠性高的双向语音交互，从而增强了这些功能。 这些 bot 可以使用 Microsoft 的语言理解 (LUIS) 进行自然语言交互。 使用语音软件开发工具包 (SDK) 的设备可以访问直接线路语音。

   ![直接连线语音业务流程服务流的概念图](media/voice-first-virtual-assistants/overview.png "语音通道流")


对于自定义的语音优先虚拟助手, 直接行语音及其相关功能是[虚拟助手解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的理想补充。 尽管直接行语音可以与任何兼容机器人配合使用, 但这些资源为高质量的对话体验提供了可重用的基线, 并为快速入门提供了常见的支持技能和模型。


## <a name="core-features"></a>核心功能

| 类别 | 功能 |
|----------|----------|
|[自定义唤醒词](speech-devices-sdk-create-kws.md) | 可以让用户使用自定义关键字 (例如 "你好 Contoso") 开始与 bot 对话。 此任务通过语音 SDK 中的自定义唤醒字词引擎来完成, 可以使用[可在此处生成](speech-devices-sdk-create-kws.md)的自定义唤醒词进行配置。 直接连线语音频道包括服务端唤醒字验证, 可改善唤醒词激活与独立设备的准确性。
|[语音到文本](speech-to-text.md) | 直接连线语音频道使用来自 Azure 语音服务的[语音到文本,](speech-to-text.md)将音频实时进入识别的文本。 当你的机器人和客户端应用程序转录时, 可以使用此文本。
|[文本到语音转换](text-to-speech.md) | 来自机器人的文本响应将使用来自 Azure 语音服务的[文本到语音转换](text-to-speech.md)。 此合成将作为音频流提供给客户端应用程序。 Microsoft 提供了构建你自己的自定义优质神经 TTS 语音的功能, 可为你的品牌提供一项语音, 以了解更多[联系我们](mailto:mstts@microsoft.com)。
|[直接连线语音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | 作为 Bot 框架内的通道, 直接线路语音实现了客户端应用程序、兼容机器人和 Azure Speech Services 功能之间的平稳且无缝连接。 若要详细了解如何将机器人配置为使用 Direct Line Speech 通道, 请参阅[机器人框架文档中的页面](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)。

## <a name="get-started-with-virtual-assistants"></a>虚拟助手入门

我们提供了快速入门, 旨在让你在不到10分钟的时间内运行代码。 此表包含按语言组织的按语音排列的虚拟助手快速入门的列表。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>示例代码

GitHub 上提供了用于创建语音优先虚拟助手的示例代码。 这些示例涵盖了用几种常用编程语言连接到机器人的客户端应用程序。

* [语音首次虚拟助手示例 (SDK)](https://aka.ms/csspeech/samples)
* [教程：语音通过语音 SDK 启用机器人,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>教程
本教程介绍如何[使用语音 SDK 和直接线路语音通道通过语音启用机器人](tutorial-voice-enable-your-bot-speech-sdk.md)。

## <a name="customization"></a>自定义

使用 Azure 语音服务构建的语音首次虚拟助手可以使用各种自定义选项, 这些选项可用于[语音到文本](speech-to-text.md)、[文本到语音](text-to-speech.md)和[自定义关键字选择](speech-devices-sdk-create-kws.md)。

> [!NOTE]
> 自定义选项因语言/区域设置而异 (请参阅[支持的语言](supported-languages.md))。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
* [创建和部署基本机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [获取虚拟助手解决方案和企业模板](https://github.com/Microsoft/AI)
