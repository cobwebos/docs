---
title: 语音助手-语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用 Speech Software 开发工具包 (SDK) 的语音助手的功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 4bdca8921a99db764cbc8ee1edfdad8cf8b8568f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360933"
---
# <a name="what-is-a-voice-assistant"></a>什么是语音助手？

使用语音服务的语音助手使开发人员可以为其应用程序和体验创建自然、人为的对话接口。

语音助手服务在设备和助手实现之间提供快速、可靠的交互，使用 (1) [直接线路语音](direct-line-speech.md) (通过 Azure 机器人服务) 将语音功能添加到机器人，或 (2) 用于语音命令方案的自定义命令。

## <a name="choosing-an-assistant-solution"></a>选择助手解决方案

创建语音助手的第一步是确定其用途。 语音服务提供了多种补充性解决方案，用于编制助手交互。 你可以将语音输入和语音功能添加到使用 Azure Bot 服务生成的灵活且功能多样的机器人，并使用 [直接 Line Speech](direct-line-speech.md) 通道，或利用为简单的语音命令方案创作 [自定义命令](custom-commands.md) 应用的简易性。

| 如果你想要... | 然后考虑 .。。 | 例如… |
|-------------------|------------------|----------------|
|具有强大技能集成和完全部署控制的开放式对话 | 带有 [直接线路语音](direct-line-speech.md) 通道的 Azure 机器人服务机器人 | <ul><li>"我需要去西雅图"</li><li>"我可以订购哪种比萨饼？"</li></ul>
|通过简化的创作和托管，提供语音命令或简单的面向任务的对话 | [自定义命令](custom-commands.md) | <ul><li>"打开高架灯"</li><li>"使其成为5度</li><li>[此处提供](https://speech.microsoft.com/customcommands)的其他示例</li></ul>

如果你还不确定希望你的助手处理哪种情况，我们建议将 [Line Speech](direct-line-speech.md) 作为最佳的默认选择。 它与一组丰富的工具和创作辅助工具（例如 [虚拟助手解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) ）以及用于基于常见模式构建并使用现有知识源的 [QnA Maker 服务](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) 相集成。

通过[自定义命令](custom-commands.md)，可以轻松地构建经过优化的丰富语音命令应用程序，以实现语音首次交互体验。 它提供统一的创作体验、自动托管模型和相对较低的复杂性，从而帮助你集中精力为语音命令方案构建最佳解决方案。

   ![助手解决方案的比较](media/voice-assistants/assistant-solution-comparison.png "助手解决方案的比较")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>使用语音 SDK 生成语音助手的参考体系结构

   ![语音助手业务流程服务流的概念图](media/voice-assistants/overview.png "语音助手流")

## <a name="core-features"></a>核心功能

无论你选择 " [直接行语音](direct-line-speech.md) " 还是 " [自定义命令](custom-commands.md) " 来创建助手交互，你都可以使用一组丰富的自定义功能来自定义你的助理、产品和个性。

| 类别 | 功能 |
|----------|----------|
|[Custom 关键字](speech-devices-sdk-create-kws.md) | 用户可以使用 "你好 Contoso" 等自定义关键字开始与助手对话。 应用使用 Speech SDK 中的自定义关键字引擎来完成此工作，可以使用 [可在此处生成](speech-devices-sdk-create-kws.md)的自定义关键字对其进行配置。 语音助手可以使用服务端关键字验证来提高关键字激活 (与独立) 设备的准确性。
|[语音转文本](speech-to-text.md) | 语音助手使用语音服务的 [语音到文本](speech-to-text.md) 将实时音频转换为识别的文本。 此文本在助手实现和客户端应用程序中转录。
|[文本到语音转换](text-to-speech.md) | 使用来自语音服务的 [文本到语音转换](text-to-speech.md) ，来自助手的文本响应。 然后，此合成将作为音频流提供给客户端应用程序。 Microsoft 提供了构建你自己的自定义优质神经 TTS 声音的功能，可为你的品牌提供一种语音。 若要了解详细[信息，请联系我们。](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>语音助手入门

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。 此表包含按语言组织的语音助手快速入门列表。

* [快速入门：使用直线语音创建自定义语音助手](quickstarts/voice-assistants.md)
* [快速入门：使用自定义命令生成语音命令应用](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>示例代码和教程

GitHub 上提供了用于创建语音助手的示例代码。 这些示例涵盖了用几种常用编程语言连接到助手的客户端应用程序。

* [GitHub 上的语音助手示例](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [教程：使用 c # 语音 SDK，通过语音启用使用 Azure 机器人服务生成的助手](tutorial-voice-enable-your-bot-speech-sdk.md)
* [教程：使用简单的语音命令创建自定义命令应用程序](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>自定义

使用 Azure 语音服务构建的语音助手可以使用各种自定义选项。

* [自定义语音识别](how-to-custom-speech.md)
* [自定义语音](how-to-custom-voice.md)
* [自定义关键字](custom-keyword-overview.md)

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](language-support.md)）。

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [了解有关自定义命令的详细信息](custom-commands.md)
* [了解有关直接连线语音的详细信息](direct-line-speech.md)
* [获取语音 SDK](speech-sdk.md)