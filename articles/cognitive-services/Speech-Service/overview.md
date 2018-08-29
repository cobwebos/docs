---
title: 什么是语音服务（预览）？
description: 语音服务属于 Microsoft 认知服务，其中合并了多个以前单独提供的 Azure 语音服务：必应语音（包括语音识别和文本转语音）、自定义语音和语音翻译。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "41936737"
---
# <a name="what-is-the-speech-service-preview"></a>什么是语音服务（预览）？

借助一个订阅，开发人员可利用语音服务轻松将强大的语音支持功能添加到应用程序。 应用现可支持语音命令、听录、听写、语音合成和语音翻译。

语音服务由其他 Microsoft 产品（包括 Cortana 和 Microsoft Office）中使用的技术提供支持。

> [!NOTE]
> 语音服务目前提供公共预览版。 请定期返回此处获取文档更新、更多示例代码等内容。

## <a name="speech-service-features"></a>语音服务功能

|函数|Description|
|-|-|
|[语音转文本](speech-to-text.md)| 将音频流转录为应用程序可以接受为输入的文本。 可与[语言理解服务](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) 集成，用于从话语派生用户意向。|
|[文本转语音](text-to-speech.md)| 将纯文本转换为发音自然的语音，将音频文件发送到应用程序。 我们为多种支持语言提供了多种语音（不同性别或口音）。 |
|[语音翻译](speech-translation.md)| 可用于几乎实时翻译流式音频或者处理录音。 |
|自定义语音转文本|可以通过创建自己的[声学](how-to-customize-acoustic-models.md)和[语言](how-to-customize-language-model.md)模型并指定自定义的[发音](how-to-customize-pronunciation.md)规则，来自定义语音转文本。 |
|[自定义文本转语音](how-to-customize-voice-font.md)|可为文本转语音创建自己的语音。|
|[语音设备 SDK](speech-devices-sdk.md)| 随着一体化语音服务的推出，Microsoft 及其合作伙伴为开发语音支持设备提供了优化的集成硬件/软件平台 |

## <a name="access-to-the-speech-service"></a>访问语音服务

可通过两种方式使用语音服务。 [SDK](speech-sdk.md) 将抽象化网络协议的细节，以便于在支持的平台上进行开发。 [REST API](rest-apis.md) 适用于任何编程语言，但不提供 SDK 提供的所有功能。

|<br>方法|语音<br>转文本|文本转<br>语音|语音<br>翻译|<br>Description|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|是|否|是|适用于特定编程语言中的库使用基于 Websocket 的协议来简化开发。|
|[REST](rest-apis.md)|是|是|否|一个基于 HTTP 的简单 API，可用于轻松将语音添加到应用程序。|

## <a name="speech-scenarios"></a>语音方案

下面简要介绍语音技术的几种常见用途。 [Speech SDK](speech-sdk.md) 是其中大多数方案的核心。

> [!div class="checklist"]
> * 创建语音触发的应用
> * 转录呼叫中心录制内容
> * 实现语音机器人

### <a name="voice-triggered-apps"></a>语音触发的应用

语音输入是使应用变得灵活、免持和快速可用的极佳办法。 在支持语音的应用中，用户只需请求所需的信息，而无需通过单击或点击导航到该信息。

如果应用面向公众，你可以使用语音服务提供的基线语音识别模型。 该模型能够很好地识别典型环境中的各种发声对象。

如果应用在特定的领域（例如医疗或 IT）中使用，则你可以创建一个[语言模型](how-to-customize-language-model.md)，向语音服务传授应用使用的特殊术语。

如果应用在嘈杂的环境（例如工厂）中使用，则你可以创建一个自定义的[声学模型](how-to-customize-acoustic-models.md)，使语音服务能够更好地将话语与噪声区分开来。

入门非常简单，只需下载[语音 SDK](speech-sdk.md) 并遵循相关的[快速入门](quickstart-csharp-dotnet-windows.md)文章即可。

### <a name="transcribe-call-center-recordings"></a>转录呼叫中心录制内容

通常，仅当来电中提出了某个问题时，才会查阅呼叫中心的录制内容。 借助语音服务，可以轻松将所有录制内容转录为文本。 转换为文本后，可以轻松为其编制索引以执行[全文搜索](https://docs.microsoft.com/azure/search/search-what-is-azure-search)，或应用[文本分析](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/)来检测情绪、语言和关键短语。

如果呼叫中心录制内容经常包含专用术语（例如产品名称或 IT 行话），则你可以创建一个[语言模型](how-to-customize-language-model.md)来向语音服务传授这些词汇。 自定义[声学模型](how-to-customize-acoustic-models.md)可帮助语音服务识别欠佳的电话连接。

有关此方案的详细信息，请详细阅读语音服务的[批处理脚本](batch-transcription.md)。

### <a name="voice-bots"></a>语音机器人

[机器人](https://dev.botframework.com/)是让用户接触到他们所需的信息，或者让客户接触到他们所热衷的业务的一种日益流行的方式。 将聊天用户界面添加到网站或应用，可使其功能更易于查找和更快地访问。 借助语音服务，这种聊天能以全新的流畅度进行，因为该技术能够真正使用合成语音对讲述的查询做出响应。

若要将独特的个性化特点添加到支持语音的机器人（并增强自己的品牌），可在机器人中提供其自己的语音。 创建自定义语音的过程包括两个步骤。 首先[录制](record-custom-voice-samples.md)想要使用的语音。 然后，[将这些录制内容（以及文本脚本）提交](how-to-customize-voice-font.md)到语音服务的[语音自定义门户](https://cris.ai/Home/CustomVoice)，后者会完成剩余的工作。 创建自定义语音后，即可直接在应用中使用它。

## <a name="next-steps"></a>后续步骤

获取语音服务的订阅密钥。

> [!div class="nextstepaction"]
> [免费试用语音服务](get-started.md)
