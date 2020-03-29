---
title: 语音助理 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音软件开发工具包 （SDK） 的语音助理的功能、功能和限制概述。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369890"
---
# <a name="what-is-a-voice-assistant"></a>什么是语音助手？

使用语音服务的语音助理使开发人员能够为其应用程序和体验创建自然、类似人类的对话界面。

语音助理服务在设备和辅助实现之间提供快速、可靠的交互，该实现使用 （1） Bot Framework 的直线语音通道或 （2） 集成的自定义命令（预览）服务来完成任务。

应用程序使用语音软件开发工具包 （SDK） 连接到语音助理服务。

   ![语音助理业务流程的概念图](media/voice-assistants/overview.png "语音助理流")

## <a name="choosing-an-assistant-solution"></a>选择助理解决方案

创建语音助理的第一步是决定它应该做什么。 语音服务提供多个互补解决方案，用于制作您的助理交互。 无论您是想要机器人框架的[直线语音](direct-line-speech.md)通道提供的灵活性和多功能性，还是希望为简单方案提供[自定义命令（预览）](custom-commands.md)的简单性，选择正确的工具都将让您入门。

| 如果你想要... | 然后考虑... | 例如... |
|-------------------|------------------|----------------|
|具有强大技能集成和全面部署控制的开放式对话 | 机器人框架的[直线语音](direct-line-speech.md)通道 | <ul><li>"我需要去西雅图"</li><li>"我可以点什么样的披萨？</li></ul>
|命令和控制或面向任务的对话，简化创作和托管 | [自定义命令（预览）](custom-commands.md) | <ul><li>"打开顶灯"</li><li>"让它温暖5度"</ul>

如果您还不确定您希望您的助手处理什么，我们建议您将[直接在线语音](direct-line-speech.md)作为最佳默认选择。 它提供了与一组丰富的工具和创作辅助工具（如[虚拟助理解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)以及[QnA Maker 服务](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview)）的集成，以构建通用模式并使用现有知识源。

[自定义命令（预览版）](custom-commands.md)提供了专为自然语言命令和控制方案量身定制的简化的创作和托管体验。

   ![助理解决方案的比较](media/voice-assistants/assistant-solution-comparison.png "助理解决方案的比较")

## <a name="core-features"></a>核心功能

无论您选择["直线语音"](direct-line-speech.md)还是["自定义命令（预览"）](custom-commands.md)来创建助理交互，您都可以使用一组丰富的自定义功能来自定义您的助手，以满足您的品牌、产品和个性。

| 类别 | 功能 |
|----------|----------|
|[Custom 关键字](speech-devices-sdk-create-kws.md) | 用户可以使用自定义关键字（如"嘿 Contoso"）开始与助手对话。 应用使用语音 SDK 中的自定义关键字引擎进行此用，该引擎可以使用自定义关键字进行配置[，您可以在此处生成](speech-devices-sdk-create-kws.md)该关键字。 语音助理可以使用服务端关键字验证来提高关键字激活的准确性（与单独使用设备相比）。
|[语音到文本](speech-to-text.md) | 语音助理使用语音服务[中的语音到文本](speech-to-text.md)将实时音频转换为识别的文本。 此文本可供助理实现和客户端应用程序使用，因为它已转录。
|[文本到语音转换](text-to-speech.md) | 使用语音服务中的["文本到语音"](text-to-speech.md)来合成助手的文本响应。 然后，此合成将作为音频流提供给客户端应用程序。 Microsoft 提供构建您自己的自定义、高质量神经 TTS 语音的能力，为您的品牌提供语音。 要了解更多信息，[请联系我们](mailto:mstts@microsoft.com)。

## <a name="getting-started-with-voice-assistants"></a>使用语音助理开始

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。 此表包括按语言组织的语音助理快速入门列表。

| 快速入门 | Platform | API 参考 |
|------------|----------|---------------|
| C#、UWP | Windows | [浏览](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [浏览](https://aka.ms/csspeech/javaref) |
| Java | Android | [浏览](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>示例代码

在 GitHub 上提供了用于创建语音助理的示例代码。 这些示例涵盖客户端应用程序，用于以几种流行的编程语言连接到您的助手。

* [语音助手示例 (SDK)](https://aka.ms/csspeech/samples)
* [教程：语音启用您的助手与语音 SDK， C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>教程

有关如何[使用语音 SDK 和直拨语音通道使助手语音启用的](tutorial-voice-enable-your-bot-speech-sdk.md)教程。

## <a name="customization"></a>自定义

使用语音服务构建的语音助手可以使用用于[语音到文本](speech-to-text.md)、[文本到语音](text-to-speech.md)和[自定义关键字选择](speech-devices-sdk-create-kws.md)的完整自定义选项。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](supported-languages.md)）。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
* [了解有关自定义命令（预览）的更多](custom-commands.md)
* [了解有关直线语音的更多内容](direct-line-speech.md)
