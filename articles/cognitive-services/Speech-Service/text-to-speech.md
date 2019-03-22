---
title: 文本到语音转换与 Azure 的语音服务
titleSuffix: Azure Cognitive Services
description: 文本到语音转换从 Azure 语音服务是一种基于 REST 的服务，使你的应用程序、 工具或设备，将文本转换为自然人合成语音。 从标准和神经声音，选择或创建你自己的自定义语音唯一的产品或品牌。 75 标准语音可在多个 45 语言和区域设置，以及 5 神经语音有 4 个语言和区域设置。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 05028704c08ebd06f9b9e4e3f45c5137eb1e6b58
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226906"
---
# <a name="what-is-text-to-speech"></a>文本到语音转换是什么？

文本到语音转换从 Azure 语音服务是一种基于 REST 的服务，使你的应用程序、 工具或设备，将文本转换为自然人合成语音。 从标准和神经语音中进行选择或创建你自己[自定义语音](#custom-voice-fonts)唯一的产品或品牌。 75 标准语音可在多个 45 语言和区域设置，以及 5 神经语音有 4 个语言和区域设置。 有关完整列表，请参阅[支持的语言](language-support.md#text-to-speech)。

文本到语音转换技术，使内容创建者可以以不同方式与他们的用户交互。 通过为用户提供用于呼叫与内容进行交互的选项，文本到语音转换可以提高可访问性。 无论用户有视觉障碍者，学习为残疾人士，还是需要导航信息，同时还能降低，文本到语音转换可以提高现有体验。 文本到语音转换也是有价值的语音机器人和虚拟助手外接程序。

### <a name="neural-voices"></a>神经语音

可以使用神经语音使与聊天机器人和虚拟助手的交流更加自然和专注、可将数字文本（如电子书）转换为有声读物以及可增强车载导航系统。 使用类似于人类的自然诗体论和明确的单词，神经语音显著减少侦听疲劳与 AI 系统进行交互时。 有关神经语音的详细信息，请参阅[支持的语言](language-support.md#text-to-speech)。

### <a name="custom-voices"></a>自定义语音

语音自定义项，可以创建自己的品牌的可识别的、 独一无二的声音。 若要创建自定义语音字体，则请 studio 录制并上传与训练数据关联的脚本。 然后，此服务会根据你的录音创建唯一的语音模型。 可以使用此自定义语音字体合成语音。 有关详细信息，请参阅[自定义语音](how-to-customize-voice-font.md)。

## <a name="core-features"></a>核心功能

此表列出了文本到语音转换的核心功能：

| 使用案例 | SDK 中 IsInRole 中的声明 | REST |
|----------|-----|------|
| 将文本到语音转换。 | 否 | 是 |
| 上传为语音适配的数据集。 | 否 | 是\* |
| 创建和管理语音字体模型。 | 否 | 是\* |
| 创建和管理语音字体部署。 | 否 | 是\* |
| 创建和管理语音字体测试。 | 否 | 是\* |
| 管理订阅。 | 否 | 是\* |

\* *这些服务可使用 cris.ai 终结点。请参阅[Swagger 引用](https://westus.cris.ai/swagger/ui/index)。*

> [!NOTE]
> 文本到语音转换的终结点实现限制，限制为 25 个每 5 秒的请求。 当出现阻止情况，则会通知您通过消息标头。

## <a name="get-started-with-text-to-speech"></a>开始使用文本到语音转换

我们提供了快速入门旨在让您在 10 分钟内运行代码。 此表包含一系列按语言的文本到语音转换快速入门。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows、 macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | 窗口中，macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Python](quickstart-python-text-to-speech.md) | 窗口中，macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |

## <a name="sample-code"></a>代码示例

文本到语音转换的示例代码位于 GitHub 上提供。 这些示例涉及最常用编程语言中的文本到语音转换。

* [文本到语音转换示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音到文本](rest-speech-to-text.md)
* [REST API：Text-to-speech](rest-text-to-speech.md)
* [REST API：批处理脚本和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [获取免费语音服务订阅](get-started.md)
* [创建自定义语音字体](how-to-customize-voice-font.md)
