---
title: 自定义命令（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 概述自定义命令的功能、功能和限制（预览），这是用于创建语音应用程序的解决方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 9349969f1f9b037a271cb727233517daba999acd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446984"
---
# <a name="custom-commands-preview"></a>自定义命令（预览版）

语音应用程序（如[语音助手](voice-assistants.md)）会倾听用户的身份，并采取措施进行响应，这通常是一回活动。 他们使用[语音到文本](speech-to-text.md)转录用户的语音，然后对文本的自然语言理解采取措施。 此操作通常包含用[文本到语音功能](text-to-speech.md)生成的助手的语音输出。 设备通过语音 SDK 的 `DialogServiceConnector` 对象连接到助手。

**自定义命令（预览版）** 是一种用于创建语音应用程序的简化解决方案。 它提供统一的创作体验、自动托管模型，以及相对较低的复杂性和其他选项，如[直接行语音](direct-line-speech.md)。 不过，这种简化的灵活性。 自定义命令（预览版）最适用于任务完成或命令和控制方案。 它对于物联网（IoT）和无外设设备特别适用。

对于复杂的对话交互以及与其他解决方案（如[虚拟助手解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)）的集成，建议使用直行语音。

适用于自定义命令（预览版）的候选词汇具有一组定义完善的变量集。 例如，家庭自动化任务（如控制恒温器）是理想之选。

   ![任务完成方案示例](media/voice-assistants/task-completion-examples.png "任务完成示例")

## <a name="getting-started-with-custom-commands-preview"></a>自定义命令入门（预览版）

使用自定义命令（预览版）发出语音应用程序的第一步是[获取语音订阅密钥](get-started.md)，并访问[speech Studio](https://speech.microsoft.com)上的自定义命令（预览版）生成器。 在这里，你可以创建一个新的自定义命令（预览版）应用程序并发布该应用程序，之后设备上的应用程序可以使用语音 SDK 与它进行通信。

   ![自定义命令的创作流（预览）](media/voice-assistants/custom-commands-flow.png "自定义命令（预览）创作流")

我们提供了快速入门，旨在让你在不到10分钟的时间内运行代码。

* [创建自定义命令（预览版）应用程序](quickstart-custom-speech-commands-create-new.md)
* [使用参数创建自定义命令（预览版）应用程序](quickstart-custom-speech-commands-create-parameters.md)
* [使用语音 SDK 连接到自定义命令（预览版）应用程序，C#](quickstart-custom-speech-commands-speech-sdk.md)

完成快速入门后，请浏览我们的操作方法。

- [向自定义命令参数添加验证](./how-to-custom-speech-commands-validations.md)
- [通过语音 SDK 完成客户端上的命令](./how-to-custom-speech-commands-fulfill-sdk.md)
- [向自定义命令添加确认](./how-to-custom-speech-commands-confirmations.md)
- [向自定义命令添加单步更正](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [转到 Speech Studio 以试用自定义命令](https://speech.microsoft.com)
* [获取语音 SDK](speech-sdk.md)
