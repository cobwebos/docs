---
title: 有关语音助手的常见问题
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
ms.openlocfilehash: 7ad3f932e9a10723d6cc1bae2fc4854c932d4c64
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507073"
---
# <a name="voice-assistants-frequently-asked-questions"></a>语音助手：常见问题

如果在本文档中找不到问题的答案，请查看[其他支持选项](support.md)。

## <a name="general"></a>常规

**问：什么是语音助手？**

**答：** 与 Cortana 一样，语音助手是一种解决方案，可侦听用户的口头最谈话，分析这些最谈话的内容，以确定其含义，执行一项或多项操作来响应查询文本的意图，并为用户提供经常的响应包含一个口述组件。 这是与系统交互的 "语音" 的 "语音" 体验。 语音助手作者使用语音 SDK 中的 `DialogServiceConnector` 创建设备上应用程序，以便与使用[自定义命令（预览版）](custom-commands.md)或机器人框架的[直接线路语音](direct-line-speech.md)通道创建的助手通信。 这些助手可以使用自定义关键字、自定义语音和自定义语音，为你的品牌或产品提供定制体验。

**问：我是否应使用自定义命令（预览）或直接语音语音？区别是什么？**

**答：** [自定义命令（预览）](custom-commands.md)是一组低复杂性的工具，可以轻松创建和承载非常适合于任务完成方案的助手。 [直接线路语音](direct-line-speech.md)提供功能更丰富且更复杂的功能，可实现强大的对话方案。 有关详细信息，请参阅[助手解决方案的比较](voice-assistants.md#comparing-assistant-solutions)。

**问：如何开始使用？**

**答：** 开始创建自定义命令（预览版）应用程序或基本机器人框架机器人的最佳方式。

* [创建自定义命令（预览版）应用程序](quickstart-custom-speech-commands-create-new.md)
* [创建基本机器人框架机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [将机器人连接到直接连线语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>调试

**问：在连接时收到401错误，无任何工作。我知道我的语音订阅密钥有效。这是怎么回事？**

**答：** 在 Azure 门户上管理你的订阅时，请确保使用**语音**资源（CognitiveServicesSpeechServices、"语音"）而*不*是**认知服务**资源（CognitiveServicesAllInOne "所有认知服务"）。 另外，请查看[语音助手的语音服务区域支持](regions.md#voice-assistants)。

![正确订阅直接行语音](media/voice-assistants/faq-supported-subscription.png "兼容的语音订阅示例")

**问：我从 `DialogServiceConnector`获取了识别文本，但看到的是 "1011" 错误，而不是我的机器人。为什么?**

**答：** 此错误表示助手和语音助手服务之间存在通信问题。

* 对于自定义命令（预览版），请确保已发布自定义命令（预览版）应用程序
* 对于直接线路语音，请确保已将[机器人连接到直接线路语音频道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)，并将[流式处理协议支持添加](https://aka.ms/botframework/addstreamingprotocolsupport)到机器人（带有相关的 Web 套接字支持），然后检查机器人是否响应传入的请求通道。

**问：此代码仍不起作用，并且/或者在使用 `DialogServiceConnector`时，我收到了不同的错误。我该怎么办？**

**答：** 基于文件的日志记录提供了更详细的信息，可帮助加速支持请求。 若要启用此功能，请参阅[如何使用文件日志记录](how-to-use-logging.md)。

## <a name="next-steps"></a>后续步骤

* [故障排除](troubleshooting.md)
* [发行说明](releasenotes.md)
