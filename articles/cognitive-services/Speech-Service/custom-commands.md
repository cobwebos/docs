---
title: 自定义命令（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义命令（预览）的功能、功能和限制概述，自定义命令是创建语音应用程序的解决方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367833"
---
# <a name="what-are-custom-commands-preview"></a>什么是自定义命令（预览）？

语音应用程序（如[语音助理](voice-assistants.md)）会侦听用户并执行响应操作，通常还会回话。 他们使用[语音到文本](speech-to-text.md)来转录用户的演讲，然后对文本的自然语言理解采取行动。 此操作通常包括使用[文本到语音](text-to-speech.md)生成的助手的语音输出。 设备使用语音 SDK`DialogServiceConnector`的对象连接到助手。

**自定义命令（预览）** 是创建语音应用程序的简化解决方案。 它提供了一个统一的创作体验，一个自动托管模型，并相对较低的复杂性比其他选项，如[直接线语音](direct-line-speech.md)。 然而，这种简化伴随着灵活性的降低。 因此，自定义命令（预览）最适合任务完成或命令和控制方案。 它特别适合物联网 （IoT） 和无头设备。

对于复杂的对话交互和与其他解决方案（如[虚拟助理解决方案和企业模板）](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的集成，我们鼓励您使用直接在线语音。

自定义命令（预览）的良好候选者具有固定的词汇表和定义良好的变量集。 例如，家庭自动化任务（如控制恒温器）是理想的。

   ![任务完成方案示例](media/voice-assistants/task-completion-examples.png "任务完成示例")

## <a name="getting-started-with-custom-commands-preview"></a>开始使用自定义命令（预览）

使用自定义命令（预览）创建语音应用程序的第[一步是获取语音订阅密钥](get-started.md)并访问[语音工作室](https://speech.microsoft.com)上的自定义命令（预览）生成器。 从那里，您可以创作新的自定义命令（预览）应用程序并发布它，之后设备上的应用程序可以使用语音 SDK 与其通信。

   ![自定义命令的创作流（预览）](media/voice-assistants/custom-commands-flow.png "自定义命令（预览）创作流")

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。

* [创建自定义命令（预览）应用程序](quickstart-custom-speech-commands-create-new.md)
* [使用参数创建自定义命令（预览）应用程序](quickstart-custom-speech-commands-create-parameters.md)
* [使用语音 SDK（C） 连接到自定义命令（预览）应用程序#](quickstart-custom-speech-commands-speech-sdk.md)

完成快速入门后，探索我们的如何处理。

- [将验证添加到自定义命令参数](./how-to-custom-speech-commands-validations.md)
- [使用语音 SDK 在客户端上执行命令](./how-to-custom-speech-commands-fulfill-sdk.md)
- [向自定义命令添加确认](./how-to-custom-speech-commands-confirmations.md)
- [向自定义命令添加单步更正](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [转到语音工作室尝试自定义命令](https://speech.microsoft.com)
* [获取语音 SDK](speech-sdk.md)
