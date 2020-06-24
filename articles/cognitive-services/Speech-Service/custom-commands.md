---
title: 自定义命令-语音服务
titleSuffix: Azure Cognitive Services
description: 概述自定义命令的功能、功能和限制，这是用于创建语音应用程序的解决方案。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9954e4fe739c055a00c2e3bc52b73200a7824a43
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85299103"
---
# <a name="what-is-custom-commands"></a>什么是自定义命令？

语音应用程序（如[语音助手](voice-assistants.md)）会倾听用户的身份，并采取措施进行响应，这通常是一回活动。 他们使用[语音到文本](speech-to-text.md)转录用户的语音，然后对文本的自然语言理解采取措施。 此操作通常包含用[文本到语音功能](text-to-speech.md)生成的助手的语音输出。 设备通过语音 SDK 对象连接到助手 `DialogServiceConnector` 。

通过**自定义命令**，可以轻松地构建经过优化的丰富语音命令应用程序，以实现语音首次交互体验。 它提供统一的创作体验、自动托管模型和相对较低的复杂性，从而帮助你集中精力为语音命令方案构建最佳解决方案。

自定义命令最适用于任务完成或命令和控制方案，尤其适合物联网（IoT）设备、环境和无外设设备。 示例包括用于宾馆、零售和汽车行业的解决方案，使你能够为你的客人构建最佳的带外语音控制体验、在商店中管理库存并在移动时控制汽车内功能。

> [!TIP]
> 在登陆页面上查看我们的示例演示 [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) 。

如果你有兴趣构建复杂的对话应用，则建议你使用[虚拟助手解决方案](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)尝试机器人框架。 可以使用直行语音将语音添加到任何机器人框架机器人。

适用于自定义命令的候选项具有一个具有定义完善的变量集的固定词汇。 例如，家庭自动化任务（如控制恒温器）是理想之选。

   ![任务完成方案示例](media/voice-assistants/task-completion-examples.png "任务完成示例")

## <a name="getting-started-with-custom-commands"></a>自定义命令入门

自定义命令的目标是减少认知负载，以了解所有不同的技术，并专注于构建语音命令应用。 使用自定义命令<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建 Azure 语音资源 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>的第一步。 您可以在 Speech Studio 上创作自定义命令应用程序并发布该应用程序，之后设备上的应用程序可以使用语音 SDK 与它进行通信。

#### <a name="authoring-flow-for-custom-commands"></a>自定义命令的创作流
   ![自定义命令的创作流](media/voice-assistants/custom-commands-flow.png "自定义命令创作流")

遵循我们的快速入门，让第一个自定义命令应用在10分钟内运行代码。

* [使用自定义命令创建语音助手](quickstart-custom-commands-application.md)

完成快速入门后，请浏览我们的操作方法指南，了解用于设计、开发、调试、部署和集成自定义命令应用程序的详细步骤。

## <a name="building-voice-assistants-with-custom-commands"></a>用自定义命令构建语音助手
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [查看 GitHub 上的语音助手存储库以获取示例](https://aka.ms/speech/cc-samples)
* [转到 Speech Studio 以试用自定义命令](https://speech.microsoft.com/customcommands)
* [获取语音 SDK](speech-sdk.md)
