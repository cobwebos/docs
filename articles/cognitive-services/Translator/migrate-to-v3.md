---
title: 迁移到 V3-转换器
titleSuffix: Azure Cognitive Services
description: 本文提供的步骤可帮助你从 V2 迁移到 Azure 认知服务转换器。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 8fae863c03ccbc17e9ec6621e73ddf475f759569
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996204"
---
# <a name="translator-v2-to-v3-migration"></a>转换器 V2 到 V3 迁移

> [!NOTE]
> V2 已于2018年4月30日弃用。 请将你的应用程序迁移到 V3，以便充分利用 V3 中专门提供的新功能。
> 
> Microsoft Translator 中心将在 2019 5 月17日停用。 [查看重要的迁移信息和日期](https://www.microsoft.com/translator/business/hub/)。  

Microsoft Translator 团队已发布转换器的版本3（V3）。 此版本包括新的功能、弃用了一些方法，并提供与 Microsoft Translator 服务相互发送和接收数据时所用的新格式。 本文档提供有关将应用程序更改为使用 V3 的信息。 

本文档末尾包含有用的链接，便于你了解详细信息。

## <a name="summary-of-features"></a>功能摘要

* 无跟踪 - 在 V3 中，“无跟踪”将应用到 Azure 门户中的所有定价层。 此功能意味着，Microsoft 不会保存提交到 V3 API 的任何文本。
* JSON - XML 将由 JSON 取代。 发送到服务的所有数据以及从服务接收的所有数据将采用 JSON 格式。
* 单个请求中的多个目标语言-转换方法接受多个 "to" 语言以便在单个请求中进行转换。 例如，单个请求可能是 "从" 英语和 "到" 德语、西班牙语和日语，或者任何其他语言组。
* 双语字典 - 已将一个双语字典方法添加到 API。 此方法包括 "lookup" 和 "示例"。
* 音译 - 已将一个 transliterate 方法添加到 API。 此方法会将一个脚本（例如阿拉伯语） 中的单词和句子转换成另一个脚本（例如 拉丁语）。
* 语言-一种新的 "语言" 方法以 JSON 格式提供语言信息，用于 "翻译"、"字典" 和 "直译" 方法。
* 新增了翻译-新功能已添加到 "翻译" 方法，以支持作为单独方法在 V2 API 中使用的某些功能。 示例包括 TranslateArray。
* Microsoft Translator 不再支持讲述方法文本到语音功能。 [Microsoft 语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)中提供了文本转语音功能。

以下 V2 和 V3 方法列表指明了能够提供 V2 随附的功能的 V3 方法和 API。

| V2 API 方法   | V3 API 兼容性 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [语言](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [语言](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft 语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft 语音服务](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | 不再支持的功能       |
| `AddTranslationArray`    | 不再支持的功能          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | 不再支持的功能         |
| `GetTranslationsArray`      | 不再支持的功能         |

## <a name="move-to-json-format"></a>改为 JSON 格式

Microsoft Translator 翻译 V2 接受并返回 XML 格式的数据。 在 V3 中，使用 API 发送和接收的所有数据采用 JSON 格式。 在 V3 中，不再接受或返回 XML 数据。

此项更改会影响针对 V2 文本翻译 API 编写的应用程序的多个方面。 例如：语言 API 返回文本翻译、音译和两个字典方法的语言信息。 可以通过一次调用请求所有方法的所有语言信息，或单独请求这些方法的信息。

languages 方法不需要身份验证；单击以下链接可以看到 V3 的 JSON 格式的所有语言信息：

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation，dictionary，音译](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>身份验证密钥

V3 接受对 V2 所用的身份验证密钥。 不需要获取新订阅。 在为期一年的迁移时间段内，可以在应用中混合使用 V2 和 V3，因此可以更轻松地发布新版本，同时逐步地从 V2-XML 迁移到 V3-JSON。

## <a name="pricing-model"></a>定价模型

Microsoft Translator V3 的定价方式与 V2 相同：按字符（包括空格）计费。 在哪些字符应该计费这一方面，V3 中的新功能做了一些改变。

| V3 方法   | 应计费的字符 |
|:----------- |:-------------|
| `Languages`     | 未提交任何字符：不计算字符数，免费。          |
| `Translate`     | 根据提交翻译的字符数，以及字符要翻译成的语言数来统计费用。 如果提交了 50 个字符并 5 请求翻译成 5 种语言，则费用是 50x5。           |
| `Transliterate`     | 根据提交音译的字符数统计费用。         |
| `Dictionary lookup & example`     | 根据提交请求字典查找和示例的字符数统计费用。         |
| `BreakSentence`     | 免费。       |
| `Detect`     | 免费。      |

## <a name="v3-end-points"></a>V3 终结点

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API 文本翻译方法

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>兼容性和自定义

> [!NOTE]
> 
> Microsoft Translator 中心将在 2019 5 月17日停用。 [查看重要的迁移信息和日期](https://www.microsoft.com/translator/business/hub/)。   

Microsoft Translator V3 默认使用神经机器翻译。 因此，它不能与 Microsoft Translator Hub 一起使用。 Translator Hub 仅支持传统的统计机器翻译。 现在，可以使用 Custom Translator 对神经翻译进行自定义。 [详细了解如何自定义神经机器翻译](custom-translator/overview.md)

使用 V3 文本 API 的神经翻译不支持使用标准类别（SMT、语音、技术、generalnn）。

| |端点|    GDPR 处理器符合性|    使用 Translator Hub|    使用自定义翻译器（预览版）|
|:-----|:-----|:-----|:-----|:-----|
|翻译版本2|    api.microsofttranslator.com|    否    |是    |否|
|翻译版本3|    api.cognitive.microsofttranslator.com|    是|    否|    是|

**翻译版本3**
* 已正式发布且完全受支持。
* 作为处理器符合 GDPR，并满足所有 ISO 20001、20018 以及 SOC 3 认证要求。 
* 可用于调用已使用自定义翻译器（预览版，新的 Translator NMT 自定义功能）自定义的神经网络翻译系统。 
* 不提供对使用 Microsoft Translator Hub 创建的自定义翻译系统的访问权限。

如果使用的是 api.cognitive.microsofttranslator.com 终结点，则使用的是版本3的转换器。

**翻译版本2**
* 不满足所有 ISO 20001、20018 和 SOC 3 认证要求。 
* 不可用于调用已使用 Translator 自定义功能自定义的神经网络翻译系统。
* 提供对使用 Microsoft Translator Hub 创建的自定义翻译系统的访问权限。
* 如果使用的是 api.microsofttranslator.com 终结点，则使用转换器版本2。

转换器的任何版本都不会创建你的翻译记录。 永远不会与任何人共享你的翻译。 有关详细信息，请参阅 [Translator 无跟踪](https://www.aka.ms/NoTrace)网页。

## <a name="links"></a>链接

* [Microsoft 隐私政策](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal)
* [联机服务术语](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看 V3.0 文档](reference/v3-0-reference.md)
