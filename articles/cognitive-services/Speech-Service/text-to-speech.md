---
title: 关于文本转语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 文本转语音 API 提供超过 45 种语言和区域设置中的 75 种以上语音。 若要使用标准语音字体，只需在调用语音服务时使用几个其他参数指定语音名称。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b413bd65582dceadd9aab912694c3b560070c4f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251548"
---
# <a name="about-the-text-to-speech-api"></a>关于文本转语音 API

文本转语音 (TTS) API 将输入文本转换为自然语音（也称为“语音合成”）。

若要生成语音，应用程序需要将 HTTP POST 请求发送到文本转语音 API。 其中，文本将合成为人工语音，并作为音频文件返回。 支持各种语音和语言。

采用语音合成的场景包括：

* *提高可访问性：* 文本转语音技术使内容所有者和发布者响应人员与其内容进行交互的不同方式。 有视觉障碍或阅读困难的人士会非常感谢能够以听觉的方式使用内容。 此外，语音输出使人们可以在上下班或健身时更加轻松地享用文本内容，如报纸或博客。

* *在多任务方案中响应：* 文本转语音让人们在驾车或在不便阅读的环境下快速而轻松地汲取重要信息。 导航是这一领域的常见应用。

* *使用多种模式增强学习：* 不同的人有不同的学习方法。 在线学习专家已经证明，同时提供语音和文本可帮助更轻松地了解和保留信息。

* *提供直观机器人或助手：* 对话功能可以是智能聊天机器人或虚拟助手的必要组成部分。 越来越多的公司正在开发聊天机器人，为客户提供具备吸引力的客户服务体验。 语音通过允许以听觉方式接收机器人响应（例如，通过电话）来添加另一个维度。

## <a name="voice-support"></a>语音支持

Microsoft 文本转语音服务提供超过 45 种语言和区域设置的 75 种以上语音。 若要使用这些标准的“语音字体”，只需在调用服务的 REST API 时使用几个其他参数指定语音名称。 若要详细了解支持的语言、区域设置和语音，请参阅[支持的语言](language-support.md#text-to-speech)。

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的费用各不相同。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="neural-voices"></a>神经语音

使用神经版文本转语音可使与聊天机器人和虚拟助手的交流更加自然和专注、可将数字文本（如电子书）转换为有声读物以及可增强车载导航系统。 随着类人的自然韵律和字词的清晰发音，在与 AI 系统交互时，神经 TTS 显著减轻了听力疲劳。 有关神经语音的详细信息，请参阅[支持的语言](language-support.md#text-to-speech)。

### <a name="custom-voices"></a>自定义语音

使用文本转语音自定义功能可以创建可识别的特色语音，该语音带有你的特色：语音字体。 若要创建语音字体，请先进行录音，然后将关联的脚本以训练数据的形式上传。 然后，此服务会根据你的录音创建唯一的语音模型。 然后，就可以使用此语音字体来合成语音。 有关更多信息，请参阅[自定义语音字体](how-to-customize-voice-font.md)。

## <a name="api-capabilities"></a>API 功能

文本转语音 API 的许多功能（特别是围绕自定义的功能）可以通过 REST 获得。 下表总结了每种访问 API 的方法的功能。 有关功能和 API 详细信息的完整列表，请参阅 [Swagger 参考](https://westus.cris.ai/swagger/ui/index)。

| 使用案例 | REST | SDK |
|-----|-----|-----|----|
| 上传数据集以进行语音适应 | 是 | 否 |
| 创建和管理语音字体模型 | 是 | 否 |
| 创建和管理语音字体部署 | 是 | 否 |
| 创建和管理语音字体测试| 是 | 否 |
| 管理订阅 | 是 | 否 |

> [!NOTE]
> API 实现限制，将 API 请求限制为每 5 秒 25 次。 消息标头将通知限制。

## <a name="next-steps"></a>后续步骤

* [获取免费语音服务订阅](https://azure.microsoft.com/try/cognitive-services/)
* [快速入门：将文本转换到语音，Python](quickstart-python-text-to-speech.md)
* [快速入门：将文本转换到语音，.NET Core](quickstart-dotnet-text-to-speech.md)
* [REST API 参考](rest-apis.md)
