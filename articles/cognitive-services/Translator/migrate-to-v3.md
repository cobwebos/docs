---
title: Microsoft 文本翻译 API - 迁移到 V3 | Microsoft Docs
description: 了解如何将文本翻译 API 从 V2 迁移到 V3。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366595"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>将 Microsoft 文本翻译 API V2 迁移到 V3

Microsoft Translator 团队已发布文本翻译 API 版本 3 (V3)。 此版本包括新的功能、弃用了一些方法，并提供与 Microsoft Translator 服务相互发送和接收数据时所用的新格式。 本文档提供有关将应用程序更改为使用 V3 的信息。 V2 已于 2018 年 4 月 30 日弃用，并将在 2019 年 4 月 30 日彻底淘汰。

本文档末尾包含有用的链接，便于你了解详细信息。

## <a name="summary-of-features"></a>功能摘要

* 无跟踪 - 在 V3 中，“无跟踪”将应用到 Azure 门户中的所有定价层。 这意味着，Microsoft 不会保存提交到 V3 API 的任何文本。
* JSON - XML 将由 JSON 取代。 发送到服务的所有数据以及从服务接收的所有数据将采用 JSON 格式。
* 单个请求中的多个目标语言 - Translate 方法接受单个请求中的多个翻译“目标”语言。 例如，单个请求可以是“从”英语翻译“到”德语、西班牙语和日语，或任何其他语言组。
* 双语字典 - 已将一个双语字典方法添加到 API。 此方法包括“查找”和“示例”。
* 音译 - 已将一个 transliterate 方法添加到 API。 此方法会将一个脚本（例如阿拉伯语） 中的单词和句子转换成另一个脚本（例如 拉丁语）。
* 语言 - 新的“languages”方法会提供 JSON 格式的语言信息，以便与“translate”、“dictionary”和“transliterate”方法结合使用。
* 新的翻译功能 - 已将新功能添加到“translate”方法，以支持 V2 API 中作为单独方法提供的某些功能。 示例包括 TranslateArray。
* 讲述方法 - Microsoft Translator API 不再支持文本转语音功能。 文本转语音功能在 Microsoft Azure 认知服务必应语音 API 中提供。

以下 V2 和 V3 方法列表指明了能够提供 V2 随附的功能的 V3 方法和 API。

| V2 API 方法   | V3 API 兼容性 |
|:----------- |:-------------|
| 翻译     | 翻译          |
| TranslateArray      | 翻译          |
| GetLanguageNames      | 语言          |
| GetLanguagesForTranslate     | 语言        |
| GetLanguagesForSpeak      | 认知服务语音 API         |
| 讲述     | 认知服务语音 API          |
| 检测     | 检测         |
| DetectArray     | 检测         |
| AddTranslation     | Microsoft Translator HUB API         |
| AddTranslationArray    | Microsoft Translator HUB API          |
| BreakSentences      | 断句         |
| GetTranslations      | 不再支持的功能         |
| GetTranslationsArray      | 不再支持的功能         |

## <a name="move-to-json-format"></a>改为 JSON 格式

Microsoft Translator 文本翻译 V2 接受 XML 格式的数据，并以此格式返回数据。 在 V3 中，使用 API 发送和接收的所有数据采用 JSON 格式。 在 V3 中，不再接受或返回 XML 数据。 

此项更改会影响针对 V2 文本翻译 API 编写的应用程序的多个方面。 例如：语言 API 返回文本翻译、音译和两个字典方法的语言信息。 可以通过一次调用请求所有方法的所有语言信息，或单独请求这些方法的信息。

languages 方法不需要身份验证；单击以下链接可以看到 V3 的 JSON 格式的所有语言信息：

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>身份验证密钥

V3 接受对 V2 所用的身份验证密钥。 不需要获取新订阅。 在为期一年的迁移时间段内，可以在应用中混合使用 V2 和 V3，因此可以更轻松地发布新版本，同时逐步地从 V2-XML 迁移到 V3-JSON。

## <a name="pricing-model"></a>定价模型

Microsoft Translator V3 的定价方式与 V2 相同：按字符（包括空格）计费。 在哪些字符应该计费这一方面，V3 中的新功能做了一些改变。

| V3 方法   | 应计费的字符 |
|:----------- |:-------------|
| 语言     | 未提交任何字符：不计算字符数，免费。          |
| 翻译     | 根据提交翻译的字符数，以及字符要翻译成的语言数来统计费用。 如果提交了 50 个字符并 5 请求翻译成 5 种语言，则费用是 50x5。           |
| 直译     | 根据提交音译的字符数统计费用。         |
| 字典查找和示例     | 根据提交请求字典查找和示例的字符数统计费用。         |
| 断句     | 无费用。       |
| 检测     | 免费。      |

## <a name="v3-end-points"></a>V3 终结点

全局

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3 API 文本翻译方法

[语言](reference/v3-0-languages.md)

[Translate](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Detect](reference/v3-0-detect.md)

[Dictionary/lookup](reference/v3-0-dictionary-lookup.md)

[Dictionary/example](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>自定义

Microsoft Translator V3 默认使用神经机器翻译。 因此，它不能与 Microsoft Translator Hub 配合使用，后者仅支持传统的统计机器翻译。 现在，可以使用 Custom Translator 对神经翻译进行自定义。 [详细了解如何自定义神经机器翻译](customization.md)

使用 V3 文本 API 的神经翻译不支持使用标准类别（smt、speech、text、generalnn）。


## <a name="links"></a>链接

* [Microsoft 隐私政策](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal)
* [联机服务术语](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看 V3.0 文档](reference/v3-0-reference.md)
