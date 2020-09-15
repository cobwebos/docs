---
title: 什么是翻译器？ - 翻译
titlesuffix: Azure Cognitive Services
description: 将“翻译”集成到应用程序、网站、工具和其他解决方案中，提供多语言用户体验。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 206e1bc86404fa41b84647c50482dbe63301c0af
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425953"
---
# <a name="what-is-translator"></a>什么是翻译器？

Azure 认知服务翻译是一项基于云的机器翻译服务，是认知 API 的 [Azure 认知服务](https://docs.microsoft.com/azure/?pivot=products&panel=ai)系列的一部分，用于生成智能应用。 “翻译”可以轻松地集成到应用程序、网站、工具和解决方案中。 通过它，你可以添加[超过 70 种语言](languages.md)的多语言用户体验，并且可以在具有任何操作系统的任何硬件平台上将其应用于文本到文本语言翻译。

## <a name="about-microsoft-translator"></a>关于 Microsoft Translator

Azure 认知服务翻译是基于云的机器翻译服务。 “翻译”为各种 Microsoft 产品和服务提供支持，并已在全球数千家企业的应用程序和工作流中使用，使其内容可传播到全球的受众。

语音翻译由“翻译”提供支持，也可通过 [Microsoft 语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/)提供。 它将语音翻译 API 和自定义语音服务的功能组合成了一项统一的可完全自定义的服务。 

## <a name="language-support"></a>语言支持

Microsoft Translator 为翻译、直译、语言检测和字典提供多语言支持。 请参阅[语言支持](language-support.md)以获取完整的列表，或者通过 [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) 以编程方式访问列表。  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator 神经机器翻译

神经机器翻译 (NMT) 是采用 AI 的高质量机器翻译的新标准。 它代替的是旧式统计机器翻译 (SMT) 技术，该技术在 2010-2020 中期的几年中达到了质量顶峰。

与 SMT 相比，NMT 不仅能够从原始翻译质量评分的立场提供更好的翻译，而且听起来更流畅、更类似于人类。 这种流畅性的主要原因在于 NMT 使用一个句子的完整语境来翻译单词。 SMT 仅考虑每个单词前面和后面几个单词的直接语境。

NMT 模型是该 API 的核心，对最终用户不可见。 唯一明显的区别是改进的翻译质量，尤其是针对中文、日语和阿拉伯语等语言。

详细了解 [NMT 的工作原理](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>语言自定义

自定义翻译器是翻译服务的扩展，可以与“翻译”配合用于自定义神经翻译系统，并改进特定术语和样式的翻译。

使用自定义翻译，可以构建翻译系统来处理自己的业务或行业中使用的术语。 然后，就可以使用类别参数通过常规的翻译将自定义翻译系统轻松集成到现有的应用程序、工作流和网站中，而且可以跨多种类型的设备。

了解有关[语言自定义](customization.md)的详细信息

## <a name="next-steps"></a>后续步骤

- [注册](translator-text-how-to-signup.md)访问密钥。
- [API 参考](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)提供了 API 的技术文档。
- [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
