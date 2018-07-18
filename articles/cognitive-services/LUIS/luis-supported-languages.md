---
title: 在 Azure 中使用 LUIS 应用支持本地化 | Microsoft Docs
description: 了解 LUIS 支持的语言。
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2017
ms.author: cahann
ms.openlocfilehash: 1eabc01ee07f8791680738a156471e3efe2c44ff
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "35366956"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>LUIS 应用中特定于区域性的理解

LUIS 应用特定于区域性，一旦设置即无法更改。 

## <a name="multi-language-luis-apps"></a>多语言 LUIS 应用
如果需要多语言 LUIS 客户端应用程序（例如聊天机器人），可通过几种方法实现。 如果 LUIS 支持所有语言，则需面向每种语言开发一个 LUIS 应用。 每个 LUIS 应用都具有唯一的应用 ID 和终结点日志。 如果需要为 LUIS 不支持的语言提供语言理解，可使用 [Microsoft Translator API](../Translator/translator-info-overview.md) 将表述翻译成受支持的语言，将表述提交到 LUIS 终结点，然后接收生成的分数。

## <a name="languages-supported"></a>支持的语言
LUIS 理解以下语言：


| 语言 |区域设置  |  预生成域 | 预生成实体 | 短语建议 | **[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | 
|--|--|:--:|:--:|:--:|:--:|
| 美国英语 |`en-US` | ✔ | ✔  |✔|✔|
| 加拿大法语 |`fr-CA` |-|   -   |-|✔|
| *[中文](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| 荷兰语 |`nl-NL` |-|  -   |-|✔|
| 法语（法国） |`fr-FR` |-| ✔ |✔ |✔|
| 德语 |`de-DE` |-| ✔ |✔ |✔|
| 意大利语 |`it-IT` |-| ✔ |✔|✔|
| *[日语](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|仅关键短语|
| 韩语 |`ko-KR` |-|   -   |-|仅关键短语|
| 葡萄牙语(巴西) |`pt-BR` |-| ✔ |✔ |并非所有亚区域性|
| 西班牙语（西班牙） |`es-ES` |-| ✔ |✔|✔|
| 西班牙语(墨西哥)|`es-MX` |-|  -   |✔|✔|


[预生成实体](luis-reference-prebuilt-entities.md)和[预生成域](luis-reference-prebuilt-domains.md)具有不同的语言支持。 

### <a name="chinese-support-notes"></a>*中文支持说明

 - 在 `zh-cn` 区域性中，LUIS 要求简体中文字符集，而不是繁体字符集。
 - 意向、实体、功能和正则表达式的名称可采用中文或罗马字符。
 - 请参阅[预生成域参考](luis-reference-prebuilt-domains.md)，了解 `zh-cn` 区域性支持的预生成域。
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*日语支持说明

 - 由于 LUIS 不提供句法分析，并且不能理解敬语和非正式日语之间的差异，因此需要将不同的正式程度作为培训示例整合到应用程序中。 
     - でございます 与 です 不同。 
     - です 与 だ 不同。 

### <a name="text-analytics-support-notes"></a>**文本分析支持说明
仅葡萄牙语支持亚区域性：`pt-PT` 和 `pt-BR`。 主区域性级别支持所有其他区域性。 详细了解文本分析[支持的语言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)。 

### <a name="speech-api-supported-languages"></a>语音 API 支持的语言
请参阅语音[支持的语言](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode)，了解语音听写模式语言。

### <a name="bing-spell-check-supported-languages"></a>必应拼写检查支持的语言
请参阅必应拼写检查[支持的语言](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages)，了解支持的语言和状态。

## <a name="rare-or-foreign-words-in-an-application"></a>应用程序中的罕见字词或外来字词
在 `en-us` 区域性中，LUIS 可学习区分大多数英文字词，包括俚语。 在 `zh-cn` 区域性中，LUIS 可学习区分大多数中文字符。 如果在 `en-us` 或 `zh-cn` 中使用一个罕见字词或字符，并且 LUIS 似乎无法识别该字词或字符，则可将该字词或字符添加到[短语列表功能](luis-how-to-add-features.md)。 例如，应将超出应用程序区域性的字词（即外来字词）添加到短语列表功能。 应将此短语列表标记为不可互换，以指示罕见字词集组成 LUIS 应学会识别的类，但它们不是同义词，也不能彼此互换。

### <a name="hybrid-languages"></a>混合语言
混合语言混含两个区域性的字词，如英语和中文。 由于单个应用仅基于单个区域性，因此 LUIS 不支持此类语言。

## <a name="tokenization"></a>词汇切分
为了执行机器学习，LUIS 基于区域性将表述拆分成[词法单元](luis-glossary.md#token)。 

|语言|  每个空格或特殊字符 | 字符级|复合词|[返回的切分后的实体](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|中文||✔||✔|
|荷兰语|||✔|✔|
|美国英语|✔ ||||
|法语 (fr-FR)|✔||||
|法语 (fr-CA)|✔||||
|德语|||✔|✔|
|意大利语|✔||||
|日语||||✔|
|韩语||✔||✔|
|葡萄牙语(巴西)|✔||||
|西班牙语 (es-ES)|✔||||
|西班牙语 (es-MX)|✔||||

 