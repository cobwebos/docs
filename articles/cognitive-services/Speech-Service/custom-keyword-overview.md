---
title: 自定义关键字-语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用语音软件开发工具包（SDK）的自定义关键字的功能、功能和限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202043"
---
# <a name="what-is-a-keyword"></a>什么是关键字？

关键字是允许激活语音的单词或短语。 例如，"你好 Cortana" 是 Cortana 助手的关键字。 语音激活使用户能够通过简单地使用关键字来完全自由地与产品交互。 当你的产品持续侦听关键字时，将在用户的设备上本地处理所有音频，直到发生检测，以确保数据尽可能保持私密状态。

## <a name="core-features-of-custom-keyword"></a>自定义关键字的核心功能

通过自定义关键字的自定义、性能和集成功能，你可以自定义语音激活，以最大程度地满足产品的视觉和用户需求。

| 功能 | 说明 |
|----------|----------|
| 关键字自定义 | 作为品牌的扩展，关键字可强化你与客户建立的权益。 借助 Speech Studio 上的自定义关键字门户，你可以指定最能代表品牌的任何单词或短语。 您可以通过选择适当的发音来进一步个性化关键字，这将由生成的关键字模型来接受。
| 关键字验证 | 如果在本地检测到的关键字有很高的置信度，则会将音频发送到云，以便进一步验证用户是否使用了关键字。 关键字验证通过降低不正确的本地检测并保护用户隐私的影响，提供额外的安全层。
| 语音助手 & 语音 SDK 集成 | 可以通过语音 SDK 轻松地在设备或应用程序中集成从 Speech Studio 上的自定义关键字生成的关键字。 只需将 SDK 指向由 Speech Studio 提供的关键字模型，你的产品就会激活语音，并受关键字验证的支持。 您可以通过构建自己的[语音助手](voice-assistants.md)来完成产品的语音体验。

## <a name="get-started-with-custom-keywords"></a>自定义关键字入门

* 教程：如何[使用 Speech Studio 创建自定义关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* 教程：如何[使用 c # 通过语音 SDK 对产品进行语音激活](tutorial-voice-enable-your-bot-speech-sdk.md)
* 快速入门：[使用 c # 通用 Windows 平台上的语音 SDK 识别关键字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* 快速入门：[使用 SPEECH SDK 在 Android 上使用 Java 识别关键字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
* [了解有关语音助手的详细信息](voice-assistants.md)
