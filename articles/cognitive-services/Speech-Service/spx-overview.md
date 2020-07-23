---
title: Azure 语音 CLI
titleSuffix: Azure Cognitive Services
description: 语音 CLI 是用于在不编写任何代码的情况下使用语音服务的命令行工具。 语音 CLI 要求设置最少，并且可以很容易地开始试验语音服务的主要功能，以确定是否能够满足你的使用案例。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: 3fb0b71cbb82b3b9acad1d1ce093baa86c700a51
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800339"
---
# <a name="what-is-the-speech-cli"></a>什么是语音 CLI？

语音 CLI 是用于在不编写任何代码的情况下使用语音服务的命令行工具。 语音 CLI 要求最小的安装，并且可以很容易地开始试验语音服务的主要功能，以确定是否能够满足你的使用案例。 在几分钟内，你可以从文件目录运行简单的测试工作流（如批处理语音识别），或从文件中的字符串集合上运行文本到语音转换。 除了简单的工作流，语音 CLI 还可以投入生产，并可进行扩展以使用自动或 shell 脚本运行更大的进程 `.bat` 。

语音 SDK 中的大部分主要功能都在语音 CLI 中提供，但一些高级功能和自定义在语音 CLI 中进行了简化。 请考虑以下指南，以确定何时使用语音 CLI 或语音 SDK。

使用语音 CLI 的时间：
* 想要在安装过程中试用语音服务功能，而无需编写代码
* 对于使用语音服务的生产应用程序，你有相对简单的要求

使用语音 SDK 的时间：
* 需要在特定的语言或平台（例如 c #、Python、c + +）中集成语音服务功能
* 你有复杂的要求，可能需要高级服务请求，或者开发自定义行为（包括响应流）

## <a name="core-features"></a>核心功能

* 语音识别-从音频文件或直接从麦克风转换语音到文本，或转录记录的对话。

* 语音合成-使用文本文件的输入或从命令行直接输入来转换文本到语音转换。 使用[SSML 配置](speech-synthesis-markup.md)和[标准或神经语音](speech-synthesis-markup.md#standard-neural-and-custom-voices)自定义语音输出特征。

* 语音翻译-将源语言的音频翻译为目标语言的文本。

* 在 Azure 计算资源上运行-使用发送 SPX 命令，以便在 Azure 远程计算资源上运行 `spx webjob` 。

## <a name="get-started"></a>入门

若要开始了解语音 CLI，请参阅[基础知识文章](spx-basics.md)。 本文介绍了如何通过 SPX 运行一些基本命令，并显示了一些更高级的命令，用于运行语音到文本和文本到语音转换的批处理操作。 阅读了基本信息后，您应该已经了解了 SPX 语法，以便开始编写一些自定义命令或自动执行简单的语音操作。

## <a name="next-steps"></a>后续步骤

- [语音 CLI 基础知识](spx-basics.md)
- 如果用例更复杂，请[获取语音 SDK](speech-sdk.md)
