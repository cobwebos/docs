---
title: 关于文本转语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 文本转语音 API 提供超过 45 种语言和区域设置中的 75 种以上语音。 若要使用标准语音字体，只需在调用语音服务时使用几个其他参数指定语音名称。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: erhopf
ms.openlocfilehash: 7f01fe5c71cdd6f4c70527fcf2553374aae9a5d8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469908"
---
# <a name="about-the-text-to-speech-api"></a>关于文本转语音 API

语音服务的文本转语音 (TTS) API 将输入文本转换为自然语音（也称为“语音合成”）。

若要生成语音，应用程序需要将 HTTP POST 请求发送到语音服务。 其中，文本将合成为人工语音，并作为音频文件返回。 支持各种语音和语言。

采用语音合成的场景包括：

* 提高可访问性：文本转语音技术使内容所有者和发布者响应人员与其内容进行交互的不同方式。 有视觉障碍或阅读困难的人士会非常感谢能够以听觉的方式使用内容。 此外，语音输出使人们可以在上下班或健身时更加轻松地享用文本内容，如报纸或博客。

* 在多任务方案中响应：文本转语音让人们在驾车或在不便阅读的环境下快速而轻松地汲取重要信息。 导航是这一领域的常见应用。

* 通过多种模式提高学习能力：不同人采用不同的学习方式效果最佳。 在线学习专家已经证明，同时提供语音和文本可帮助更轻松地了解和保留信息。

* 提供直观机器人或助手：对话功能可以是智能聊天机器人或虚拟助手的必要组成部分。 越来越多的公司正在开发聊天机器人，为客户提供具备吸引力的客户服务体验。 语音通过允许以听觉方式接收机器人响应（例如，通过电话）来添加另一个维度。

## <a name="voice-support"></a>语音支持

Microsoft 文本转语音服务提供超过 45 种语言和区域设置的 75 种以上语音。 若要使用这些标准的“语音字体”，只需在调用服务的 REST API 时使用几个其他参数指定语音名称。 有关支持语音的详细信息，请参阅[支持的语言](language-support.md#text-to-speech)。

如果应用程序需要独特语音，你可以从自己的语音示例创建[自定义语音字体](how-to-customize-voice-font.md)。

## <a name="api-capabilities"></a>API 功能

**文本转语音** API 的许多功能（特别是围绕自定义的功能）可以通过 REST 获得。 下表总结了每种访问 API 的方法的功能。 有关功能和 API 详细信息的完整列表，请参阅 [Swagger 参考](https://westus.cris.ai/swagger/ui/index)。

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

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [参阅如何通过 REST API 合成语音](how-to-text-to-speech.md)
