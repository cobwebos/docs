---
title: 自定义命令（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 概述自定义命令的功能、功能和限制（预览），这是用于创建语音助手的解决方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 62210bf480d09ce2a256a44b7554ac53aa06eb0c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579704"
---
# <a name="custom-commands-preview"></a>自定义命令（预览）

[语音助手](voice-assistants.md)会倾听用户的响应，并在响应时执行操作，这通常是一回谈话。 他们使用[语音到文本](speech-to-text.md)转录用户的语音，然后对文本的自然语言理解采取措施。 此操作通常包含用[文本到语音功能](text-to-speech.md)生成的助手的语音输出。 设备通过语音 SDK 的 `DialogServiceConnector` 对象连接到助手。

**自定义命令（预览）** 是用于创建语音助手的简化解决方案。 它提供统一的创作体验、自动托管模型，以及相对较低的复杂性和其他助手创建选项（如[直接行语音](direct-line-speech.md)）。 不过，这种简化的灵活性。 自定义命令（预览版）最适用于任务完成或命令和控制方案。 它对于物联网（IoT）和无外设设备特别适用。

对于复杂的对话交互以及与其他解决方案（如[虚拟助手解决方案和企业模板](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)）的集成，建议使用直行语音。

适用于自定义命令（预览版）的候选词汇具有一组定义完善的变量集。 例如，家庭自动化任务（如控制恒温器）是理想之选。

   ![任务完成方案示例](media/voice-assistants/task-completion-examples.png "任务完成示例")

## <a name="getting-started-with-custom-commands-preview"></a>自定义命令入门（预览版）

使用自定义命令（预览版）进行语音助手的第一步是[获取语音订阅密钥](get-started.md)，并访问[speech Studio](https://speech.microsoft.com)上的自定义命令（预览版）生成器。 在这里，你可以创建一个新的自定义命令（预览版）应用程序并发布该应用程序，之后设备上的应用程序可以使用语音 SDK 与它进行通信。

   ![自定义命令的创作流（预览）](media/voice-assistants/custom-commands-flow.png "自定义命令（预览）创作流")

我们专门提供了快速入门来帮助你在 10 分钟内运行代码。

* [创建自定义命令（预览版）应用程序](quickstart-custom-speech-commands-create-new.md)
* [使用参数创建自定义命令（预览版）应用程序](quickstart-custom-speech-commands-create-parameters.md)
* [使用语音 SDK 连接到自定义命令（预览版）应用程序，C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>代码示例

GitHub 上提供了用于通过自定义命令（预览版）创建语音助手的示例代码。

* [语音助手示例（SDK）](https://aka.ms/csspeech/samples)

## <a name="customization"></a>自定义

使用 Azure 语音服务构建的语音助手可以使用各种自定义选项，这些选项可用于[语音到文本](speech-to-text.md)、[文本到语音](text-to-speech.md)和[自定义关键字选择](speech-devices-sdk-create-kws.md)。

> [!NOTE]
> 自定义选项因语言/区域设置而异（请参阅[支持的语言](supported-languages.md)）。

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
