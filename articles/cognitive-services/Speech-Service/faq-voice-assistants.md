---
title: 语音助手常见问题
titleSuffix: Azure Cognitive Services
description: 获取有关使用自定义命令（预览版）或直接连线语音通道的语音助手的最常见问题的解答。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110340"
---
# <a name="voice-assistants-frequently-asked-questions"></a>语音助手常见问题

如果在本文档中找不到问题的答案，请查看[其他支持选项](support.md)。

## <a name="general"></a>一般信息

**问：什么是语音助手？**

**答：** 与 Cortana 一样，语音助手是一种解决方案，可侦听用户的口头最谈话，分析这些最谈话的内容，以确定其含义，执行一项或多项操作来响应查询文本的意图，并为用户提供经常的响应包含一个口述组件。 这是与系统交互的 "语音" 的 "语音" 体验。 语音助手作者使用语音 SDK 中的 `DialogServiceConnector` 创建设备上应用程序，以便与使用[自定义命令（预览版）](custom-commands.md)或机器人框架的[直接线路语音](direct-line-speech.md)通道创建的助手通信。 这些助手可以使用自定义关键字、自定义语音和自定义语音，为你的品牌或产品提供定制体验。

**问：我是否应使用自定义命令（预览）或直接语音语音？区别是什么？**

**答：** [自定义命令（预览）](custom-commands.md)是一组低复杂性的工具，可以轻松创建和承载非常适合于任务完成方案的助手。 [直接线路语音](direct-line-speech.md)提供功能更丰富且更复杂的功能，可实现强大的对话方案。 有关详细信息，请参阅[助手解决方案的比较](voice-assistants.md#choosing-an-assistant-solution)。

**问：如何开始使用？**

**答：** 开始创建自定义命令（预览版）应用程序或基本机器人框架机器人的最佳方式。

- [创建自定义命令（预览版）应用程序](quickstart-custom-speech-commands-create-new.md)
- [创建基本机器人框架机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [将机器人连接到直接连线语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>调试

**问：我的频道机密在哪里？**

**答：** 如果你使用了直接行语音的预览版本或正在阅读相关文档，则可能需要在 "语音通道注册" 页上找到密钥。 Speech SDK 中的版本 1.7 `DialogServiceConfig` 工厂方法 `FromBotSecret` 也需要此值。

最新版本的直接连线语音简化了从设备联系机器人的过程。 在 "通道注册" 页上，顶部的下拉箭头会将你的直接线路语音频道注册与语音资源相关联。 关联后，v2.0 Speech SDK 包含一种 `BotFrameworkConfig::FromSubscription` 工厂方法，该方法将配置 `DialogServiceConnector`，以联系与订阅关联的机器人。

如果仍要将客户端应用程序从版本1.7 升级到5v，`DialogServiceConfig::FromBotSecret` 可能会继续使用其通道机密参数的非空、非 null 值（例如，使用的上一个机密）。 使用与较新的频道注册相关联的语音订阅时，将直接忽略此功能。 请注意，该值_必须_为非 null 和非空，因为在服务端关联之前，将在设备上检查这些值。

有关更详细的指南，请参阅[教程部分](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel)，该部分介绍了渠道注册。

**问：在连接时收到401错误，无任何工作。我知道我的语音订阅密钥有效。这是怎么回事？**

**答：** 在 Azure 门户上管理你的订阅时，请确保使用**语音**资源（CognitiveServicesSpeechServices、"语音"）而_不_是**认知服务**资源（CognitiveServicesAllInOne "所有认知服务"）。 另外，请查看[语音助手的语音服务区域支持](regions.md#voice-assistants)。

![正确订阅直接行语音](media/voice-assistants/faq-supported-subscription.png "兼容的语音订阅示例")

**问：我从 `DialogServiceConnector`获取了识别文本，但看到的是 "1011" 错误，而不是我的机器人。为什么?**

**答：** 此错误表示助手和语音助手服务之间存在通信问题。

- 对于自定义命令（预览版），请确保已发布自定义命令（预览版）应用程序
- 对于直接线路语音，请确保已将[机器人连接到直接线路语音频道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)，并将[流式处理协议支持添加](https://aka.ms/botframework/addstreamingprotocolsupport)到机器人（带有相关的 Web 套接字支持），然后检查机器人是否响应传入的请求通道。

**问：此代码仍不起作用，并且/或者在使用 `DialogServiceConnector`时，我收到了不同的错误。我该怎么办？**

**答：** 基于文件的日志记录提供了更详细的信息，可帮助加速支持请求。 若要启用此功能，请参阅[如何使用文件日志记录](how-to-use-logging.md)。

## <a name="next-steps"></a>后续步骤

- [故障排除](troubleshooting.md)
- [发行说明](releasenotes.md)
