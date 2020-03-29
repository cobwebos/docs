---
title: 呼叫中心转录 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音到文本的常见方案是转录来自各种系统（如交互式语音响应 （IVR） 的大量电话数据。 使用语音服务和统一语音模型，企业可以通过音频捕获系统获得高质量的转录。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806314"
---
# <a name="speech-service-for-telephony-data"></a>电话数据的语音服务

通过座机、手机和无线电生成的电话数据通常质量很低，处于 8 KHz 的窄频范围内，在执行语音转文本过程中会带来挑战。 语音服务的最新语音识别模型擅长转录此电话数据，即使在数据难以理解的情况下也是如此。 这些型号经过大量电话数据培训，即使在嘈杂的环境中也能获得最佳的市场识别精度。

语音转文本的一个常见应用场景是听录来自各种系统（例如交互式语音应答 (IVR)）的大量电话数据。 这些系统提供的音频可能是立体声或单音的，音频信号只经过极少量的后期处理，甚至未经任何后期处理。 使用语音服务和统一语音模型，企业可以获取高质量的转录，无论使用什么系统来捕获音频。

电话数据可用于更好地了解客户的需求，识别新的营销机会，或者评估呼叫中心座席的工作绩效。 数据转录后，企业可以将输出用于改进遥测、识别关键短语或分析客户情绪等目的。

本页中所述的技术是 Microsoft 在内部针对各种处于实时和批处理模式的电话支持处理服务开发的。

让我们回顾一下语音服务提供的一些技术和相关功能。

> [!IMPORTANT]
> 语音服务 Unified 模型经过各种数据培训，为从听写到电话分析等多种方案提供了单一模型解决方案。

## <a name="azure-technology-for-call-centers"></a>适用于呼叫中心的 Azure 技术

除了语音服务功能的功能方面，它们的主要目的（应用于呼叫中心）是改善客户体验。 在此方面存在三个明确的应用领域：

- 呼叫后分析，本质上是呼叫后呼叫录音的批处理。
- 实时分析，即处理音频信号，以在呼叫发生时提取各种见解（情绪是一个突出的用例）。
- 语音助理（机器人），要么推动客户和机器人之间的对话，试图在没有代理参与的情况下解决客户的问题，要么是应用人工智能 （AI） 协议来帮助代理。

下面的![呼叫中心听录体系结构](media/scenarios/call-center-transcription-architecture.png)插图描绘了批处理方案实现的典型体系结构示意图

## <a name="speech-analytics-technology-components"></a>语音分析技术组件

无论域是呼叫后还是实时，Azure 都提供一套成熟和新兴技术来改善客户体验。

### <a name="speech-to-text-stt"></a>语音转文本 (STT)

[语音到文本](speech-to-text.md)是任何呼叫中心解决方案中最抢手的功能。 由于许多下游分析过程依赖于转录的文本，因此单词错误率 （_WER_） 至关重要。 呼叫中心听录的主要难题之一是呼叫中心普遍存在的噪音（例如，其他座席在幕后讲话）、各种不同的语言区域性和方言，以及低质量的实际电话信号。 WER 与声学和语言模型对给定区域设置的培训程度高度相关，因此，根据区域设置自定义模型的能力非常重要。 我们的最新 4.x 版统一模型是可以改善听录准确度和延迟的解决方案。 Unified 模型经过数万小时的声学数据和数十亿条词汇信息的培训，是市场上抄录呼叫中心数据的最准确模型。

### <a name="sentiment"></a>情绪

衡量客户是否获得了良好的体验是在呼叫中心领域应用的语音分析服务的最重要功能之一。 我们的[批量听录 API](batch-transcription.md) 基于每段言语提供情绪分析。 可以聚合在听录通话过程中获取的值集，以确定座席和客户在通话时的情绪。

### <a name="silence-non-talk"></a>静默（无对话）

在支持通话中，有 35% 的时间双方无对话也是常用的事。 发生不对话的一些方案包括：代理查找客户以前的案例历史记录，使用工具允许他们访问客户的桌面并执行功能的代理，等待转移的客户等。 极为重要的是，在呼叫中判断何时发生沉默，因为这些类型的方案以及调用中发生的位置存在许多重要的客户敏感性。

### <a name="translation"></a>翻译

一些公司正在尝试提供外语支持电话的翻译记录，以便交付经理能够了解其客户的世界性体验。 我们的[翻译](translation.md)功能非常优秀。 我们可以为大量的区域设置翻译音频到音频或音频到文本。

### <a name="text-to-speech"></a>文本到语音转换

在实现可与客户交互的机器人时，[文本转语音](text-to-speech.md)是另一个重要的方面。 典型的流程是客户讲话、将客户的语音转录为文本、分析文本中的意向、基于识别的意向合成响应，然后向客户呈现一个资产，或者生成合成的语音响应。 当然，所有这些都必须迅速发生，因此低延迟是这些系统成功的重要组成部分。

我们的端到端延迟对于涉及的各种技术来说相当低，例如[语音到文本](speech-to-text.md)[、LUIS、Bot](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)[框架](https://dev.botframework.com/)、[文本到语音](text-to-speech.md)。

我们的新语音与人类语音没有区别。 您可以使用我们的声音给你的机器人其独特的个性。

### <a name="search"></a>搜索

分析的另一个重要方面是在发生特定的事件或体验时识别交互。 这通常用两种方法之一完成;用户只需键入短语和系统响应的 ad hoc 搜索，也可以是更结构化的查询，分析人员可以创建一组逻辑语句，用于标识调用中的方案，然后每个调用都可以针对该查询集编制索引。 一个很好的搜索示例是无处不在的合规性声明"此调用应记录用于质量目的...". 许多公司都希望确保其代理在实际记录呼叫之前向客户提供此免责声明。 大多数分析系统都能够预测查询/搜索算法发现的行为，而这种趋势报告最终是分析系统最重要的功能之一。 通过[认知服务目录](https://azure.microsoft.com/services/cognitive-services/directory/search/)，可以使用索引和搜索功能显著增强端到端解决方案。

### <a name="key-phrase-extraction"></a>关键短语提取

这一领域是更具挑战性的分析应用程序之一，也受益于 AI 和机器学习的应用。 在这种情况下，主要方案是推断客户意图。 客户为何来电？ 客户遇到了哪种问题？ 客户为何会受到负面的体验？ 我们的[文本分析服务](https://azure.microsoft.com/services/cognitive-services/text-analytics/)提供一套开箱即用的分析，以便快速升级用于提取这些重要关键字或短语的端到端解决方案。

现在，让我们更详细地了解一下语音识别的批处理和实时管道。

## <a name="batch-transcription-of-call-center-data"></a>批量听录呼叫中心数据

对于转录批量音频，我们开发了[批量转录 API](batch-transcription.md)。 批量听录 API 旨在以异步方式听录大量的音频数据。 关于呼叫中心数据的转录，我们的解决方案基于以下支柱：

- **精度**- 第四代统一型号，我们提供无与伦比的转录质量。
- **延迟**- 我们理解，在进行批量转录时，需要快速的转录。 通过[批量听录 API](batch-transcription.md) 启动的听录作业将立即排入队列，一旦开始运行该作业，其执行速度比实时听录更快。
- **安全性**- 我们理解呼叫可能包含敏感数据。 请放心，保证安全是我们的最高优先事务之一。 我们的服务已通过 ISO、SOC、HIPAA 和 PCI 认证。

呼叫中心每天生成大量音频数据。 如果你的企业将电话数据存储在某个中心位置（例如 Azure 存储），则你可以使用[批量听录 API](batch-transcription.md) 以异步方式请求和接收听录内容。

典型的解决方案使用以下服务：

- 语音服务用于转录语音到文本。 使用批处理转录 API 需要语音服务的标准订阅 （S0）。 免费订阅 (F0) 不可用。
- 使用 [Azure 存储](https://azure.microsoft.com/services/storage/)来存储电话数据以及批量听录 API 返回的听录内容。 此存储帐户应使用通知，特别是添加新文件时。 这些通知用于触发听录过程。
- 使用 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 来为每个录制内容创建共享访问签名 (SAS) URI，以及触发启动听录的 HTTP POST 请求。 此外，Azure Functions 用于创建通过批量听录 API 检索和删除听录内容的请求。

在内部，我们使用上述技术来支持批量模式的 Microsoft 客户呼叫。
![Batch 体系结构](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>呼叫中心数据的实时听录

某些企业需要实时听录对话。 实时听录可用于识别关键字、针对对话相关的内容和资源触发搜索、监视情绪、改善可访问性，或者为听不懂本地语言的客户和座席提供翻译。

对于需要实时听录的场景，我们建议使用[语音 SDK](speech-sdk.md)。 目前，语音转文本支持 [20 多种语言](language-support.md)，该 SDK 适用于 C++、C#、Java、Python、Node.js、Objective-C 和 JavaScript。 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 上提供了适用于每种语言的示例。 有关最新消息和更新，请参阅[发行说明](releasenotes.md)。

在内部，我们使用上述技术实时分析 Microsoft 客户呼叫发生的时间，如下图所示。

![Batch 体系结构](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>有关 IVR 的说明

语音服务可以使用[语音 SDK](speech-sdk.md)或[REST API](rest-apis.md)轻松集成到任何解决方案中。 但是，呼叫中心听录可能需要额外的技术。 通常，需要在 IVR 系统与 Azure 之间建立连接。 尽管我们不提供此类组件，但以下是与 IVR 的连接需要的描述。

多个 IVR 或电话服务产品（如 Genesys 或 AudioCodes）提供集成功能，可以利用这些功能启用入站和出站音频传递到 Azure 服务。 基本上，自定义 Azure 服务可能提供特定接口来定义电话呼叫会话（如呼叫开始或呼叫结束），并公开 WebSocket API 以接收与语音服务一起使用的入站流音频。 出站响应（例如对话听录或者与 Bot Framework 的连接）可与 Microsoft 文本转语音服务合成，并返回到 IVR 进行播放。

另一种方案是直接与会话启动协议 （SIP） 集成。 Azure 服务连接到 SIP 服务器，因此可以获取要在语音转文本和文本转语音阶段使用的入站流和出站流。 若要连接到 SIP 服务器，可以使用市售的软件产品/服务，例如 Ozeki SDK，或[团队呼叫和会议 API](/graph/api/resources/communications-api-overview)（目前为 Beta 版），它们都旨在支持此类音频呼叫方案。

## <a name="customize-existing-experiences"></a>自定义现有体验

 语音服务与内置模型配合良好。 但是，您可能需要进一步自定义和调整产品或环境的体验。 自定义选项的范围从声学模型优化，到专属于自有品牌的语音字体。 构建自定义模型后，可以在实时或批处理模式下将其与任何语音服务功能一起使用。

| 语音服务 | “模型” | 描述 |
| -------------- | ----- | ----------- |
| 语音转文本 | [声学模型](how-to-customize-acoustic-models.md) | 为特定环境（例如汽车或工厂车间）中使用的应用程序、工具或设备创建自定义声学模型，每个模型具有特定的录制条件。 示例包括带有口音的讲话、特定的背景噪音，或使用特定的麦克风录制音频。 |
|                | [语言模型](how-to-customize-language-model.md) | 创建自定义语言模型来改善行业特定的词汇和语法的听录，例如医疗术语中或 IT 行话。 |
|                | [发音模型](how-to-customize-pronunciation.md) | 使用自定义发音模型，您可以定义拼音形式并显示单词或术语。 它适用于处理自定义术语，如产品名称或首字母缩略词。 所有你需要开始是一个发音文件，这是一个简单的`.txt`文件。 |
| 文本转语音 | [语音字体](how-to-customize-voice-font.md) | 使用自定义语音字体可为自有品牌创建可识别的独一无二的声音。 只需使用少量的数据即可开始创建。 提供的数据越多，语音字体就越自然，且越接近人类语音。 |

## <a name="sample-code"></a>示例代码

对于每个语音服务功能，GitHub 上都有示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。 使用以下链接查看 SDK 和 REST 示例：

- [语音转文本和语音翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>参考文档

- [语音 SDK](speech-sdk-reference.md)
- [语音设备 SDK](speech-devices-sdk.md)
- [REST API：语音到文本](rest-speech-to-text.md)
- [REST API：文本到语音](rest-text-to-speech.md)
- [REST API：批处理转录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [免费获取语音服务订阅密钥](get-started.md)
