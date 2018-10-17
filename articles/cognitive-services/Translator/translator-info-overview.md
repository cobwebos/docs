---
title: 什么是文本翻译 API？
titlesuffix: Azure Cognitive Services
description: 将文本翻译 API 集成到应用程序、网站、工具和其他解决方案中，提供多语言用户体验。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: overview
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: 47ac3c81b85424a5cacab00863cd88fab9463fb9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124654"
---
# <a name="what-is-translator-text-api"></a>什么是文本翻译 API？

文本翻译 API 可以无缝集成到应用程序、网站、工具或其他解决方案中，提供 [60 多种语言](languages.md)的多语言用户体验。 它可以在任何硬件平台上使用，以及在任何操作系统中使用，用于执行文本到文本语言翻译。

文本翻译 API 是云中机器学习和 AI 算法的 Azure [认知服务 API](https://docs.microsoft.com/azure/#pivot=products&panel=ai) 集合的一部分，可随时在开发项目中使用。

## <a name="about-microsoft-translator"></a>关于 Microsoft Translator

Microsoft Translator 是基于云的机器翻译服务。 此服务的核心是文本翻译 API 和[语音翻译 API](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-translation)，这些 API 为各种 Microsoft 产品和服务提供支持，并已在全球数千家企业的应用程序和工作流中使用，使他们的内容可传播到全球的受众。

语音翻译功能可以通过[认知服务语音预览版](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/)获取，它将现有的语音翻译 API、必应语音 API 和自定义语音服务（预览版）组合成了一项统一的可完全自定义的服务。  

详细了解 [Microsoft Translator 服务](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="language-customization"></a>语言自定义

自定义翻译是核心 Microsoft Translator 服务的扩展，可以与文本翻译 API 配合用于自定义神经翻译系统，并改进特定术语和样式的翻译。

使用自定义翻译，可以构建翻译系统来处理自己的业务或行业中使用的术语。 然后，就可以使用类别参数通过常规的 Microsoft 文本翻译 API 将自定义翻译系统轻松集成到现有的应用程序、工作流和网站中，而且可以跨多种类型的设备。 

了解有关[语言自定义](customization.md)的详细信息

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator 神经机器翻译

神经机器翻译 (NMT) 是采用 AI 的高质量机器翻译的新标准。 它代替的是旧式统计机器翻译 (SMT) 技术，该技术在 2010-2020 中期的几年中达到了质量顶峰。

与 SMT 相比，NMT 不仅能够从原始翻译质量评分的立场提供更好的翻译，而且听起来更流畅、更类似于人类。 这种流畅性的主要原因在于 NMT 使用一个句子的完整语境来翻译单词。 SMT 仅考虑每个单词前面和后面几个单词的直接语境。

NMT 模型是该 API 的核心，对最终用户不可见。 唯一明显的区别是改进的翻译质量，尤其是针对中文、日语和阿拉伯语等语言。 

详细了解 [NMT 的工作原理](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>后续步骤

- 了解[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。

- [注册](translator-text-how-to-signup.md)访问密钥。

- [快速入门](quickstarts/csharp.md)演练了以 C# 编写的 REST API 调用。 了解如何使用最少的代码将一种语言的文本翻译为另一种语言。

- [API 参考文档](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)提供了 API 的技术文档。

## <a name="see-also"></a>另请参阅

- [认知服务文档页](https://docs.microsoft.com/azure/#pivot=products&panel=ai)
- [认知服务产品页](https://azure.microsoft.com/services/cognitive-services/)
- [解决方案和定价信息](https://www.microsoft.com/en-us/translator/default.aspx)
