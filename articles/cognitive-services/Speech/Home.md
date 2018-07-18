---
title: Microsoft 语音服务 | Microsoft Docs
description: 使用 Microsoft 语音 API 向应用添加语音驱动的操作，包括与用户进行实时交互。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: c041132e992f07e94e4b6669ec7ce174f7c2d0dd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365669"
---
# <a name="microsoft-speech-api-overview"></a>Microsoft 语音 API 概述

使用基于云的 Microsoft 语音 API，开发人员可以轻松地在其应用程序中创建支持语音的强大功能，例如语音命令控制、使用自然语音对话的用户对话以及语音听录和听写。 Microsoft 语音 API 支持“语音转文本”和“文本转语音”转换。

- 语音转文本 API 将人类语音转换为可用作控制应用程序的输入或命令的文本。
- 文本转语音 API 将文本转换为可向应用程序用户播放的音频流。

## <a name="speech-to-text-speech-recognition"></a>语音转文本（语音识别）

Microsoft 语音识别 API 将音频流转录为应用程序可以向用户显示或作为命令输入操作的文本。 它为开发人员提供了两种将语音添加到其应用的方式：REST API 或基于 Websocket 的客户端库。

- [REST API](GetStarted/GetStartedREST.md)：开发人员可使用从其应用到服务的 HTTP 调用来进行语音识别。
- [客户端库](GetStarted/GetStartedClientLibraries.md)：对于高级功能，开发人员可下载 Microsoft 语音客户端库，并链接到其应用。  客户端库在使用不同语言（C#、Java、JavaScript、ObjectiveC）的多种平台（Windows、Android、iOS）上可用。 与 REST API 不同，客户端库使用基于 Websocket 的协议。

| 用例 | [REST API](GetStarted/GetStartedREST.md) | [客户端库](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| 转换短语音音频，例如无中间结果的命令（音频长度 < 15 秒） | 是 | 是 |
| 转换长音频（> 15 秒） | 否 | 是 |
| 流式传输具有所需中间结果的音频 | 否 | 是 |
| 了解使用 LUIS 从音频转换的文本 | 否 | 是 |

无论开发人员选择哪种方法（REST API 或客户端库），Microsoft 语音服务都支持以下内容：

- Cortana、Office 听写、Office 翻译工具和其他 Microsoft 产品中使用的 Microsoft 高级语音识别技术。
- 实时连续识别。 语音识别 API 使用户能够实时将音频听录为文本，并支持接收目前为止已识别字词的中间结果。 语音服务还支持语音结束检测。 此外，用户可选择其他格式功能，例如大写和标点符号、屏蔽不当字词和文本规范化。
- 支持已针对交互、对话和听写场景优化的语音识别结果。 对于需要自定义语言模型和声学模型的用户方案，[自定义语音服务](../custom-speech-service/cognitive-services-custom-speech-home.md)允许创建适合应用程序和用户的语音模型。
- 支持多种方言中的多种口语。 有关每种识别模式下支持的语言的完整列表，请参阅[识别语言](api-reference-rest/supportedlanguages.md)。
- 与语言理解集成。 除了将输入音频转换为文本外，语音转文本还为应用程序提供了理解文本含义的附加功能。 它使用[语言理解智能服务 (LUIS)](../LUIS/Home.md) 从识别文本中提取意向和实体。

### <a name="next-steps"></a>后续步骤

- 利用 [REST API](GetStarted/GetStartedREST.md) 或[客户端库](GetStarted/GetStartedClientLibraries.md)，开始使用 Microsoft 语音识别服务。
- 使用首选编程语言测试[示例应用程序](samples.md)。
- 有关 [Microsoft 语音协议](API-Reference-REST/websocketprotocol.md)的详细信息和 API 参考，请参阅参考部分。

## <a name="text-to-speech-speech-synthesis"></a>文本转语音（语音合成）

文本转语音 API 使用 REST 将结构化文本转换为音频流。 API 提供多种语音和语言的文本到语音快速转换。 此外，用户还可以使用 SSML 标签更改发音、音量、音高 等音频特性。

### <a name="next-steps"></a>后续步骤

- 开始使用 Microsoft 文本转语音服务，请参阅[文本转语音 API 参考](api-reference-rest/bingvoiceoutput.md)。 有关文本转语音支持的语言和语音的完整列表，请参阅[支持的区域设置和语音字体](api-reference-rest/bingvoiceoutput.md#SupLocales)。
