---
title: 语音助手-语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用 Speech 软件开发工具包（SDK）的语音助手的功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: bd808c0c71e02483b4c4b06e612720c1802869a0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577982"
---
# <a name="about-voice-assistants"></a>关于语音助手

使用 Azure 语音服务的语音助手使开发人员可以为其应用程序和体验创建自然、人为的对话接口。

语音助手服务在设备和助手实现之间提供快速、可靠的交互，使用（1）机器人框架的直接线路语音通道或（2）用于任务完成的集成自定义命令（预览版）服务。

应用程序通过语音软件开发工具包（SDK）连接到语音助手服务。

   ![语音助手业务流程服务流的概念图](media/voice-assistants/overview.png "语音助手流")

## <a name="choosing-an-assistant-solution"></a>选择助手解决方案

创建语音助手的第一步是确定其用途。 Azure Speech Services 提供多种补充性解决方案，用于起草助手交互。 无论你是否希望在简单的方案中，通过机器人框架的[直接线路语音](direct-line-speech.md)通道提供的灵活性和多功能性，还是简单的[自定义命令（预览）](custom-commands.md) ，选择合适的工具即可开始。

| 是否要... | 然后考虑 。 | 例如 。 |
|-------------------|------------------|----------------|
|具有强大技能集成和完全部署控制的开放式对话 | 机器人框架的[直接连线语音](direct-line-speech.md)通道 | <ul><li>"我需要去西雅图"</li><li>"我可以订购哪种比萨饼？"</li></ul>
|通过简化的创作和托管来进行命令和控制或面向任务的对话 | [自定义命令（预览）](custom-commands.md) | <ul><li>"打开高架灯"</li><li>"使其成为5度</ul>

如果你还不确定希望你的助手处理哪种情况，我们建议将[Line Speech](direct-line-speech.md)作为最佳的默认选择。 它与一组丰富的工具和创作辅助工具（例如[虚拟助手解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)）以及用于基于常见模式构建并使用现有知识源的[QnA Maker 服务](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview)相集成。

[自定义命令（预览版）](custom-commands.md)提供专门为自然语言命令和控制方案量身打造的创作和托管体验。
   ![助手解决方案的比较](media/voice-assistants/assistant-solution-comparison.png "Comp助手解决方案的 arison）

## <a name="core-features"></a>核心功能

无论你选择 "[直接行语音](direct-line-speech.md)" 还是 "[自定义命令" （预览版）](custom-commands.md)来创建助手交互，你都可以使用一组丰富的自定义功能来自定义你的助理、产品和个性。

| Category | 功能 |
|----------|----------|
|[Custom 关键字](speech-devices-sdk-create-kws.md) | 用户可以使用 "你好 Contoso" 等自定义关键字开始与助手对话。 应用使用 Speech SDK 中的自定义关键字引擎来完成此工作，可以使用[可在此处生成](speech-devices-sdk-create-kws.md)的自定义关键字对其进行配置。 语音助手可以使用服务端关键字验证来提高关键字激活的准确性（与设备独立）。
|[语音到文本](speech-to-text.md) | 语音助手使用 Azure 语音服务的[语音到文本](speech-to-text.md)将实时音频转换为可识别的文本。 此文本在助手实现和客户端应用程序中转录。
|[文本到语音转换](text-to-speech.md) | 使用来自 Azure Speech Services 的[文本到语音转换](text-to-speech.md)，来自你的助手的文本响应。 然后，此合成将作为音频流提供给客户端应用程序。 Microsoft 提供了构建你自己的自定义优质神经 TTS 声音的功能，可为你的品牌提供一种语音。 若要了解详细[信息，请联系我们。](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>语音助手入门

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。 此表包含按语言组织的语音助手快速入门列表。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| C#、UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>代码示例

GitHub 上提供了用于创建语音助手的示例代码。 这些示例涵盖了用几种常用编程语言连接到助手的客户端应用程序。

* [语音助手示例（SDK）](https://aka.ms/csspeech/samples)
* [教程：通过语音 SDK 使用语音启用助手，C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>教程

本教程介绍如何[使用语音 SDK 和直接语音通道来启用助手](tutorial-voice-enable-your-bot-speech-sdk.md)。

## <a name="customization"></a>自定义

使用 Azure 语音服务构建的语音助手可以使用各种自定义选项，这些选项可用于[语音到文本](speech-to-text.md)、[文本到语音](text-to-speech.md)和[自定义关键字选择](speech-devices-sdk-create-kws.md)。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](supported-languages.md)）。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
* [了解有关自定义命令的详细信息（预览版）](custom-commands.md)
* [了解有关直接连线语音的详细信息](direct-line-speech.md)