---
title: 什么是语音服务（预览）？
description: 语音服务属于 Microsoft 认知服务，其中合并了多个以前单独提供的 Azure 语音服务：必应语音（包括语音识别和文本转语音）、自定义语音和语音翻译。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091466"
---
# <a name="what-is-the-speech-service"></a>什么是语音服务？

语音服务合并了此前通过[必应语音 API](https://docs.microsoft.com/azure/cognitive-services/speech/home)、[语音翻译](https://docs.microsoft.com/azure/cognitive-services/translator-speech/)、[自定义语音](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home)和[自定义声音](http://customvoice.ai/)服务提供的 Azure 语音功能。 现在，可以通过一个订阅来访问所有这些功能。

与其他 Azure 语音服务一样，此语音服务由经验证的语音技术提供支持，这些技术在 Cortana 和 Microsoft Office 之类的产品中使用。 结果质量和 Azure 云的可靠性是值得依赖的。

> [!NOTE]
> 语音服务目前提供公共预览版。 请定期返回此处，以获取文档更新、新的代码示例等内容。

## <a name="main-speech-service-functions"></a>主要语音服务功能

语音服务的主要功能为语音转文本（也称为语音识别或听录）、文本转语音（语音合成）和语音翻译。

|函数|功能|
|-|-|
|[语音转文本](speech-to-text.md)| <ul><li>将连续的实时语音转换为文本。<li>可以从音频录音对语音进行批量转换。 <li>为交互、聊天和听写用例提供识别模式。<li>支持中间结果、语音结束检测、自动设置文本格式以及不雅内容屏蔽。 <li>可以通过调用[语言理解](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS)，从转换的语音获取用户意向。\*|
|[文本到语音转换](text-to-speech.md)| <ul><li>将文本转换为自然发音的语音。 <li>为许多支持的语言提供多种性别和/或方言。 <li>支持纯文本输入或语音合成标记语言 (SSML)。 |
|[语音翻译](speech-translation.md)| <ul><li>以近实时方式翻译流式传输音频<li> 也可处理记录的语音<li>以文本或合成语音的方式提供结果。 |

\* *意向识别需要 LUIS 订阅。*


## <a name="customizing-speech-features"></a>自定义语音功能

有了语音服务，就可以使用自己的数据来训练语音服务的语音转文本和文本转语音功能下的模型。 

|功能|模型|目的|
|-|-|-|
|语音转文本|[声学模型](how-to-customize-acoustic-models.md)|有助于转换特定的发言人和环境，例如汽车或工厂|
||[语言模型](how-to-customize-language-model.md)|有助于转换特定于领域的词汇和语法，例如医学或 IT 专业术语|
||[发音模型](how-to-customize-pronunciation.md)|有助于转换缩写和首字母缩写，例如“IOU”是“i oh you”的首字母缩写 |
|文本到语音转换|[语音字体](how-to-customize-voice-font.md)|根据人类语音的示例来训练模型，为应用提供它自己的语音。|

创建以后，自定义模型可以用在任何位置，只要可以在该位置使用应用的语音转文本或文本转语音功能中的标准模型。


## <a name="using-the-speech-service"></a>使用语音服务

为了简化支持语音的应用程序的开发，Microsoft 提供了可以与新的语音服务配合使用的[语音 SDK](speech-sdk.md)。 语音 SDK 提供一致的本机语音转文本和语音翻译 API，适用于 C#、C++ 和 Java。 如果使用这其中的某种语言进行开发，你会发现语音 SDK 有助于开发，因为它可以为你处理网络详细信息。

语音服务还有一个 [REST API](rest-apis.md)，此 API 适用于任何能够发出 HTTP 请求的编程语言。 但是，此 REST 接口不提供 SDK 的流式处理实时功能。

|<br>方法|语音<br>转文本|文本转<br>语音|语音<br>翻译|<br>Description|
|-|-|-|-|-|
|[语音 SDK](speech-sdk.md)|是|否|是|适用于 C#、C++ 和 Java 的本机 API，可以简化开发。|
|[REST](rest-apis.md)|是|是|否|一个基于 HTTP 的简单 API，可以轻松地将语音添加到应用程序。|

### <a name="websockets"></a>WebSockets

语音服务还有 WebSockets 协议，用于流式传输语音转文本和语音翻译内容。 语音 SDK 使用这些协议与语音服务通信。 应该使用语音 SDK，而不应尝试实现你自己与语音服务的 WebSockets 通信。

但是，如果已经有可以通过 WebSockets 使用必应搜索或语音翻译的代码，则可直接对它进行更新，使之能够使用语音服务。 WebSockets 协议是兼容的；仅终结点不同。

### <a name="speech-devices-sdk"></a>语音设备 SDK

[语音设备 SDK](speech-devices-sdk.md) 是一种集成式硬件和软件平台，适用于支持语音的设备的开发人员。 我们的硬件合作伙伴提供参考设计和开发单元。 Microsoft 提供设备优化的 SDK，该 SDK 可以充分利用硬件的功能。


## <a name="speech-scenarios"></a>语音方案

语音服务用例包括：

> [!div class="checklist"]
> * 创建语音触发的应用
> * 转录呼叫中心录制内容
> * 实现语音机器人

### <a name="voice-user-interface"></a>语音用户界面

语音输入是使应用变得灵活、免持和快速可用的极佳办法。 在支持语音的应用中，用户只需请求所需的信息，而无需导航到该信息。

如果应用面向公众，可以使用默认语音识别模型。 此类模型能够很好地识别常规环境中的各种发声对象。

如果应用在特定的领域（例如医疗或 IT）中使用，则你可以创建一个[语言模型](how-to-customize-language-model.md)，向语音服务传授应用使用的特殊术语。

如果应用在嘈杂的环境（例如工厂）中使用，则你可以创建一个自定义的[声学模型](how-to-customize-acoustic-models.md)，使语音服务能够更好地将话语与噪声区分开来。

入门非常简单，只需下载[语音 SDK](speech-sdk.md) 并遵循相关的[快速入门](quickstart-csharp-dotnet-windows.md)文章即可。

### <a name="call-center-transcription"></a>呼叫中心听录

通常，仅当来电中提出了某个问题时，才会查阅呼叫中心的录制内容。 借助语音服务，可以轻松将所有录制内容转录为文本。 转换为文本后，可以轻松为其编制索引以执行[全文搜索](https://docs.microsoft.com/azure/search/search-what-is-azure-search)，或应用[文本分析](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/)来检测情绪、语言和关键短语。

如果呼叫中心录制内容频繁使用专业术语（例如产品名称或 IT 行话），则可创建一个[语言模型](how-to-customize-language-model.md)来向语音服务传授这些词汇。 自定义[声学模型](how-to-customize-acoustic-models.md)可帮助语音服务识别欠佳的电话连接。

有关此方案的详细信息，请详细阅读语音服务的[批处理脚本](batch-transcription.md)。

### <a name="voice-bots"></a>语音机器人

[机器人](https://dev.botframework.com/)是让用户接触到他们所需的信息，或者让客户接触到他们所热衷的业务的一种日益流行的方式。 将聊天用户界面添加到网站或应用，可使其功能更易于查找和更快地访问。 借助语音服务，这种聊天能以全新的流畅度进行，能够对语音查询进行语音响应。

若要将独特的个性化特点添加到支持语音的机器人（并增强自己的品牌），可在机器人中提供其自己的语音。 创建自定义语音的过程包括两个步骤。 首先[录制](record-custom-voice-samples.md)想要使用的语音。 然后，[将这些录制内容（以及文本脚本）提交](how-to-customize-voice-font.md)到语音服务的[语音自定义门户](https://cris.ai/Home/CustomVoice)，后者会完成剩余的工作。 创建自定义语音后，即可直接在应用中使用它。

## <a name="next-steps"></a>后续步骤

获取语音服务的订阅密钥。

> [!div class="nextstepaction"]
> [免费试用语音服务](get-started.md)
