---
title: Azure 语音 CLI
titleSuffix: Azure Cognitive Services
description: 语音 CLI 是一种命令行工具，用于在不编写任何代码的情况下使用语音服务。 语音 CLI 只需要很少的设置，你能够快速开始就语音服务的关键功能进行试验，来确定其是否可以满足你的用例。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067420"
---
# <a name="what-is-the-speech-cli"></a>什么是语音 CLI？

语音 CLI 是一种命令行工具，用于在不编写任何代码的情况下使用语音服务。 语音 CLI 只需要很少的设置，并且很容易立即开始体验语音服务的关键功能，以确定是否可以满足你的用例。 在几分钟内即可运行简单的测试工作流，如对一系列文件中的语音进行批量语音识别，或对文件中的字符串集合进行文本转语音操作。 除了简单的工作流，语音 CLI 还可随时投入生产，并且可以使用自动化 `.bat` 或 shell 脚本进行纵向扩展，以运行更大的进程。

语音 SDK 中的大部分主要功能都可在语音 CLI 中使用，一些高级功能和自定义内容在语音 CLI 中进行了简化。 请参考以下指南，确定何时使用语音 CLI 或语音 SDK。

适合使用语音 CLI 的情况：
* 想在极少设置且无需代码的情况下试验语音服务功能
* 对使用语音服务的生产应用程序的要求相对简单

适合使用语音 SDK 的情况：
* 想在特定语言或平台（如 C#、Python、C++）中集成语音服务功能
* 需要满足复杂的要求，可能需要高级服务请求，或者开发自定义行为（包括响应流式处理）

## <a name="core-features"></a>核心功能

* 语音识别 - 将音频文件中的或直接将麦克风语音转换为文本，或转录录制的对话。

* 语音合成 - 将文本文件中输入的文本或直接来自于命令行的输入文本转换为语音。 使用 [SSML 配置](speech-synthesis-markup.md)和[标准语音或神经语音](speech-synthesis-markup.md#standard-neural-and-custom-voices)来自定义语音输出特征。

* 语音翻译 - 将源语言的音频翻译为目标语言的文本或音频。

* 在 Azure 计算资源上运行 - 使用 `spx webjob` 发送语音 CLI 命令以在 Azure 远程计算资源上运行。

## <a name="get-started"></a>入门

若要开始使用语音 CLI，请参阅[基础知识文章](spx-basics.md)。 本文介绍如何运行一些基本命令，并显示了用于运行批处理操作以实现语音转文本和文本转语音的一些高级命令。 阅读基础知识文章之后，你应已对此语法有了足够的了解，可以开始编写一些自定义命令或自动执行简单的语音服务操作。

## <a name="next-steps"></a>后续步骤

- [语音 CLI 基础知识](spx-basics.md)
- 如果用例较复杂，请[获取语音 SDK](speech-sdk.md)
