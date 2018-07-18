---
title: 语音翻译 API 文档 | Microsoft Docs
titleSuffix: Cognitive Services
description: 使用 Microsoft Translator 语音翻译 API 将“语音转语音”和“语音转文本”翻译添加到应用程序。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365744"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft 语音翻译 API
不管目标 OS 或开发语言是什么，都可以使用 Microsoft 语音翻译 API 将端到端的实时语音翻译添加到需要多语言语音翻译的应用程序、工具或任何解决方案。 该 API 可用于“语音转语音”和“语音转文本”翻译。

Microsoft 文本翻译 API 是一个 Azure 服务，属于云中机器学习和 AI 算法的 [Microsoft 认知服务 API 集合](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)，随时可在开发项目中使用。

借助 Microsoft 语音翻译 API，客户端应用程序可将语音音频流式传输到服务，和接收基于文本和音频的结果，包括在源语言中识别到的文本及其目标语言翻译。 通过将深度神经网络支持的自动语音识别 (ASR) 应用到音频流，来生成文本结果。 名为 TrueText 的新技术会进一步改善原始 ASR 输出，以更准确地反映用户意向。 例如，TrueText 会删除不流畅的语流（“嗯”和咳嗽声）和重复的单词，并还原正确的标点和大小写。 此外，还包括掩码或排除亵渎字词的功能。 识别和翻译引擎经过专门的训练，可以处理对话语音。 

语音翻译服务使用寂静检测技术来确定陈述的末尾。 在语音活动停顿后，服务会流式传回已完成的陈述的最终结果。 服务还能发回部分结果，对进行中的陈述执行中间识别和翻译。 

对于语音转语音翻译，服务可提供从目标语言中的讲述文本合成语音的功能（文本转语音）。 文本转语音音频以客户端指定的格式创建。 可以使用 WAV 和 MP3 格式。

语音翻译 API 使用 WebSocket 协议在客户端和服务器之间提供全双工信道。 

## <a name="about-microsoft-translator"></a>关于 Microsoft Translator
Microsoft Translator 是基于云的机器翻译服务。 此服务的核心是 [文本翻译 API] (https://www.microsoft.com/en-us/translator/translatorapi.aspx) 和语音翻译 API，这些 API 为各种 Microsoft 产品和服务提供支持，并已在全球数千家企业的应用程序和工作流中使用，使他们的内容可传播到全球的受众。

详细了解 [Microsoft Translator 服务](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Microsoft Translator 神经机器翻译 (NMT)
Microsoft 语音翻译 API 使用传统的统计机器翻译 (SMT) 和新式神经机器翻译 (NMT) 来提供翻译。

统计机器翻译在性能改善方面停滞不前。 使用 SMT 时，翻译质量在普通的系统上不再有明显的改善。 基于神经网络 (NN) 的新式 AI 翻译技术正在成为主流。

与 SMT 相比，NMT 不仅能够从原始翻译质量评分的立场提供更好的翻译，而且听起来更流畅、更类似于人类。 这种流畅性的主要原因在于 NMT 使用一个句子的完整语境来翻译单词。 SMT 仅考虑每个单词前面和后面几个单词的直接语境。

NMT 模型是该 API 的核心，对最终用户不可见。 两者的明显差别在于：
* 改善翻译质量，尤其是针对中文、日语和阿拉伯语等语言。
* 与现有的 Hub 自定义功能（适用于 Microsoft 文本翻译 API）不兼容

所有支持的语音翻译语言由 NMT 提供支持。 因此，所有语音转语音翻译都使用 NMT。 

语音转文本翻译可以根据语言对使用 NMT 和 SMT 的组合。 如果 NMT 支持目标语言，则 NMT 支持全译。 如果 NMT 不支持目标语言，则翻译是 NMT 和 SMT 的结合，将英语作为两种语言之间的“枢轴”。 

在 [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx) 上查看支持的语言。 

详细了解 [NMT 的工作原理](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [注册](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [开始编写代码](quickstarts/csharp.md)

## <a name="see-also"></a>另请参阅
- [认知服务文档页](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [认知服务产品页](https://azure.microsoft.com/services/cognitive-services/)
- [解决方案和定价信息](https://www.microsoft.com/en-us/translator/home.aspx) 
