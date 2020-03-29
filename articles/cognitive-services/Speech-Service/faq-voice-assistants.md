---
title: 语音助理常见问题
titleSuffix: Azure Cognitive Services
description: 使用自定义命令（预览）或直拨语音通道获取有关语音助理的最常见问题的答案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110340"
---
# <a name="voice-assistants-frequently-asked-questions"></a>语音助理常见问题

如果您在本文档中找不到问题的答案，请查看[其他支持选项](support.md)。

## <a name="general"></a>常规

**问：什么是语音助理？**

**答：** 与 Cortana 一样，语音助理是一种解决方案，用于侦听用户的语音陈述，分析这些话语的内容以表达意义，执行一个或多个操作以响应话语的意图，然后向通常包含语音组件的用户提供响应。 它是与系统交互的"语音、语音"体验。 语音助理作者使用语音 SDK`DialogServiceConnector`中创建设备应用程序，以便与使用[自定义命令（预览）](custom-commands.md)或自动框架[的直线语音](direct-line-speech.md)通道创建的助手进行通信。 这些助手可以使用自定义关键字、自定义语音和自定义语音来提供根据您的品牌或产品量身定制的体验。

**问：我应该使用自定义命令（预览）还是直接行语音？有什么区别？**

**答：**[自定义命令（预览）](custom-commands.md)是一组较低复杂性的工具，用于轻松创建和托管非常适合任务完成方案的助理。 [直接在线语音](direct-line-speech.md)提供更丰富、更复杂的功能，可实现可靠的对话方案。 有关详细信息[，请参阅助理解决方案的比较](voice-assistants.md#choosing-an-assistant-solution)。

**问：如何开始使用？**

**答：** 从创建自定义命令（预览）应用程序或基本机器人框架机器人开始的最佳方式。

- [创建自定义命令（预览）应用程序](quickstart-custom-speech-commands-create-new.md)
- [创建基本机器人框架机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [将自动程序连接到直线语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>调试

**问：我的频道机密在哪里？**

**答：** 如果您已使用"直行语音"的预览版本，或者正在阅读相关文档，则可能需要在"直线语音"频道注册页面上找到一个机密密钥。 语音 SDK 中的`DialogServiceConfig`v1.7 工厂方法`FromBotSecret`也期望此值。

最新版本的"直线语音"简化了从设备联系机器人的过程。 在频道注册页面上，顶部的下拉将您的直接线路语音频道注册与语音资源关联。 一旦关联，v1.8 语音 SDK`BotFrameworkConfig::FromSubscription`包括一个工厂方法`DialogServiceConnector`，该方法将配置 以与订阅关联的机器人联系。

如果仍在将客户端应用程序从 v1.7 迁移到 v1.8，`DialogServiceConfig::FromBotSecret`则可能会继续使用非空、非空的值来处理其通道机密参数，例如您使用的前一个机密。 使用与较新的通道注册关联的语音订阅时，将忽略它。 请注意，该值_必须_为非空和非空值，因为在服务端关联相关之前，将在设备上检查这些值。

有关更详细的指南，请参阅浏览频道注册的[教程部分](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel)。

**问：连接时出现 401 错误，因此没有任何问题有效。我知道我的语音订阅密钥是有效的。这是怎么回事？**

**答：** 在 Azure 门户上管理订阅时，请确保使用的是**语音**资源（Microsoft.CognitiveServices语音服务，"语音"），_而不是_**认知服务**资源（Microsoft.CognitiveServicesAllInOne，"所有认知服务"）。 此外，请检查[语音助理语音服务区域支持](regions.md#voice-assistants)。

![正确订阅直线语音](media/voice-assistants/faq-supported-subscription.png "兼容语音订阅的示例")

**问：我从 中收到识别文本`DialogServiceConnector`，但我看到"1011"错误，从我的机器人中什么也看不到。为什么？**

**答：** 此错误表示您的助理和语音助理服务之间的通信问题。

- 对于自定义命令（预览），请确保发布自定义命令（预览）应用程序
- 对于直接线路语音，请确保将[自动程序连接到直接线路语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)，[将流式处理协议支持添加到](https://aka.ms/botframework/addstreamingprotocolsupport)您的自动程序（具有相关的 Web 套接字支持），然后检查您的自动程序是否响应来自该通道的传入请求。

**问：此代码仍然不起作用，并且/或者我在使用 时`DialogServiceConnector`收到不同的错误。我该怎么办？**

**答：** 基于文件的日志记录提供了更多的详细信息，并有助于加快支持请求。 要启用此功能，请参阅[如何使用文件日志记录](how-to-use-logging.md)。

## <a name="next-steps"></a>后续步骤

- [疑难解答](troubleshooting.md)
- [发行说明](releasenotes.md)
