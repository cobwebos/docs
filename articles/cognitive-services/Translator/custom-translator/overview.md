---
title: 什么是自定义翻译？
titleSuffix: Azure Cognitive Services
description: 自定义翻译提供的功能类似于 Microsoft Translator Hub 为统计机器翻译 (SMT) 提供的功能，但专用于神经机器翻译 (NMT) 系统。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: 04f90292b74593dece5f7e54268907e184fd084c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592757"
---
# <a name="what-is-custom-translator"></a>什么是自定义翻译？

[自定义翻译](https://portal.customtranslator.azure.ai)是 Microsoft Translator 服务的一项功能，允许翻译企业、应用开发人员和语言服务提供者生成自定义的神经机器翻译 (NMT) 系统。 自定义的翻译系统可无缝集成到现有的应用程序、工作流和网站中。 [自定义翻译](https://portal.customtranslator.azure.ai/)提供的功能类似于 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 为统计机器翻译 (SMT) 提供的功能，但专用于神经机器翻译 (NMT) 系统。

使用[自定义翻译](https://portal.customtranslator.azure.ai)生成的翻译系统可以通过同一个基于云、[安全](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)、高效且高度可缩放的 Microsoft 文本[翻译 API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 来使用，该 API 版本每天为数十亿条翻译提供支持。

自定义翻译支持三十多种语言，可以直接映射到适用于 NMT 的语言。 如需完整的列表，请参阅 [Microsoft Translator 语言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)。

## <a name="features"></a>功能

自定义翻译提供不同的功能，用于生成自定义翻译系统，然后即可对其进行访问。

|Feature  |说明  |
|---------|---------|
|[利用神经机器翻译技术](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  利用自定义翻译提供的神经机器翻译 (NMT) 来改进翻译。       |
|[生成了解业务术语的系统](what-are-parallel-documents.md)     |  使用并行文档自定义并生成翻译系统，该系统了解在你自己的业务和行业中使用的术语。       |
|[使用字典来生成模型](what-is-dictionary.md)     |   如果没有训练数据集，可以只使用字典数据训练一个模型。       |
|[与他人协作](how-to-manage-settings.md#share-your-workspace)     |   将工作与他人共享，与团队协作。     |
|[访问自定义翻译模型](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  可以通过翻译 V3 使用现有的应用程序/程序来随时访问自定义翻译模型。       |

## <a name="get-better-translations"></a>获取更好的翻译

Microsoft Translator 在 2016 年发布了[神经机器翻译 (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)。 NMT 的翻译质量相对于行业标准的[统计机器翻译 (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) 技术有很大的进步。 由于 NMT 在翻译之前能够更好地理解完整句子的上下文，因此其提供的翻译质量更高、更类似于人类且更流畅。 [自定义翻译](https://portal.customtranslator.azure.ai)为自定义模型提供 NMT，因此翻译质量更好。

可以使用以前翻译的文档生成一个翻译系统。 这些文档包括特定于领域的术语和样式，比标准翻译系统更好。 用户可以上传 ALIGN、PDF、LCL、HTML、HTM、XLF、TMX、XLIFF、TXT、DOCX 和 XLSX 文档。

自定义翻译也接受在文档级别并行的数据，使数据收集和准备更有效。 如果用户可以访问相同内容的多种语言版（但每种语言的内容都位于单独的文档中），自定义翻译将能够跨文档自动匹配句子。

如果提供的训练数据类型和数量适当，则在使用自定义翻译时，常常可以看到获得的 [BLEU 分数](what-is-bleu-score.md)在 5 到 10 分之间。

## <a name="be-productive-and-cost-effective"></a>提高工作效率和成本效益

使用[自定义翻译](https://portal.customtranslator.azure.ai)时，训练和部署自定义系统不需任何编程技能。

使用安全的[自定义翻译](https://portal.customtranslator.azure.ai)门户时，用户可以通过一个直观的用户界面来上传训练数据、训练系统和测试系统，并将其部署到生产环境。 然后，系统会在数小时内（实际时间取决于训练数据大小）可供大规模使用。

[自定义翻译](https://portal.customtranslator.azure.ai)也可通过[专用 API](https://custom-api.cognitive.microsofttranslator.com/swagger/)（目前为预览版）以编程方式进行访问。 有了该 API，用户就可以通过自己的应用或 Web 服务定期对训练的创建和更新进行管理。

使用自定义模型来翻译内容时，其价格取决于用户的翻译定价层。 如需定价层详细信息，请参阅认知服务[翻译定价网页](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>随时随地在所有应用和服务上进行安全的翻译

可以在任何设备上使用标准的 REST 技术通过“翻译”以无缝方式访问自定义系统，并将其集成到任意产品或业务工作流。

## <a name="next-steps"></a>后续步骤

- 了解[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。

- 通过[快速入门](quickstart-build-deploy-custom-model.md)了解如何在自定义翻译中生成翻译模型。
