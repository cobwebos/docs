---
title: 自定义关键字-语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用语音软件开发工具包 (SDK) 的自定义关键字的功能、功能和限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356598"
---
# <a name="what-is-a-keyword"></a>什么是关键字？

关键字是允许激活语音的单词或短语。 例如，"你好 Cortana" 是 Cortana 助手的关键字。 语音激活使用户能够通过简单地使用关键字来完全自由地与产品交互。 当你的产品持续侦听关键字时，将在用户的设备上本地处理所有音频，直到发生检测，以确保数据尽可能保持私密状态。

## <a name="core-features-of-custom-keyword"></a>自定义关键字的核心功能

通过自定义关键字的自定义、性能和集成功能，你可以自定义语音激活，以最大程度地满足产品的视觉和用户需求。

| Feature | 说明 |
|----------|----------|
| 关键字自定义 | 作为品牌的扩展，关键字可强化你与客户建立的权益。 借助 Speech Studio 上的自定义关键字门户，你可以指定最能代表品牌的任何单词或短语。 您可以通过选择适当的发音来进一步个性化关键字，这将由生成的关键字模型来接受。
| 关键字验证 | 如果在本地检测到的关键字有很高的置信度，则会将音频发送到云，以便进一步验证用户是否使用了关键字。 关键字验证通过降低不正确的本地检测并保护用户隐私的影响，提供额外的安全层。
| 语音助手 & 语音 SDK 集成 | 可以通过语音 SDK 轻松地在设备或应用程序中集成从 Speech Studio 上的自定义关键字生成的关键字。 只需将 SDK 指向由 Speech Studio 提供的关键字模型，你的产品就会激活语音，并受关键字验证的支持。 您可以通过构建自己的 [语音助手](voice-assistants.md)来完成产品的语音体验。

## <a name="get-started-with-custom-keywords"></a>自定义关键字入门

* 有关基本用法和设计模式，请参阅 [自定义关键字基础知识](custom-keyword-basics.md) 。
* 如何 [使用 c # 通过语音 SDK 对产品进行语音激活](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="choose-an-effective-keyword"></a>选择有效关键字

创建有效的关键字对于确保你的设备能够一致准确地做出响应至关重要。 自定义关键字是区分设备并增强品牌的有效方法。 选择关键字时，请考虑以下准则：

> [!div class="checklist"]
> * 关键字应为英语单词或短语。
> * 说该词应该不超过两秒钟。
> * 4 到 7 个音节的单词效果最好。 例如，"你好，Computer" 是一个不错的关键字。 而只是“Hey”则是一个糟糕的唤醒词。
> * 关键字应遵循常见的英语发音规则。
> * 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，"computerama" 可能是一个不错的关键字。
> * 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。
> * 避免使用可能包含备用发音的关键字。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。
> * 不要使用特殊字符、符号或数字。 例如，"中转 #" 和 "20 + 猫" 可能是问题的关键字。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果选择 "商标字" 一词作为关键字，请确保你拥有该商标，或者你拥有商标所有者的许可才能使用此词。 对于您选择的关键字可能产生的任何法律问题，Microsoft 不承担任何责任。

## <a name="see-samples-on-github"></a>请参阅 GitHub 上的示例

* [使用语音 SDK 识别关键字，使用 C 通用 Windows 平台#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [使用 Speech SDK 在 Android 上使用 Java 识别关键字](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
* [获取语音 SDK](speech-sdk.md)
* [了解有关语音助手的详细信息](voice-assistants.md)
