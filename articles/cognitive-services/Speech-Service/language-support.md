---
title: 语言支持-语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务支持多种语言进行语音到文本和文本到语音转换，同时提供语音翻译。 本文提供按服务功能列出的语言支持的综合列表。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b5f227deb3385d64160f5a469d76b9763057b160
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381046"
---
# <a name="language-and-region-support-for-the-speech-service"></a>语音服务的语言和区域支持

语言支持因语音服务功能而异。 下表汇总了[语音到文本](#speech-to-text)、[文本到语音](#text-to-speech)[转换和语音翻译](#speech-translation)服务产品的语言支持。

## <a name="speech-to-text"></a>语音转文本

Microsoft Speech SDK 和 REST API 都支持以下语言（区域设置）。 若要提高准确性，可通过上传音频和人贴标签的脚本或相关文本：句子，为语言子集提供自定义项。  发音自定义当前仅适用于 `en-US` 和 `de-DE`。 [在此处](how-to-custom-speech.md)了解有关自定义的详细信息。

 区域设置 | 语言 | 受支持 | 可自定义
------|------------|-----------|-------------
`ar-EG` | 阿拉伯语(埃及)，现代标准 | 是 | 是
`ar-SA` | 阿拉伯语（沙特阿拉伯） | 是 | 是
`ar-AE` | 阿拉伯语（阿拉伯联合酋长国） | 是 | 是
`ar-KW` | 阿拉伯语（科威特） | 是 | 是
`ar-QA` | 阿拉伯语（卡塔尔） | 是 | 是
`ca-ES` | 加泰罗尼亚语 | 是 | 否
`da-DK` | 丹麦语（丹麦） | 是 | 否
`de-DE` | 德语（德国） | 是 | 是
`en-AU` | 英语（澳大利亚） | 是 | 是
`en-CA` | 英语（加拿大） | 是 | 是
`en-GB` | 英语（英国） | 是 | 是
`en-IN` | English (India) | 是 | 是
`en-NZ` | 英语（新西兰） | 是 | 是
`en-US` | 英语（美国） | 是 | 是
`es-ES` | 西班牙语(西班牙) | 是 | 是
`es-MX` | 西班牙语（墨西哥） | 是 | 是
`fi-FI` | 芬兰语（芬兰） | 是 | 否
`fr-CA` | 法语（加拿大） | 是 | 是
`fr-FR` | 法语（法国） | 是 | 是
`gu-IN` | 古吉拉特语（印度） | 是 | 是
`hi-IN` | 印地语（印度） | 是 | 是
`it-IT` | 意大利语（意大利） | 是 | 是
`ja-JP` | 日语（日本） | 是 | 是
`ko-KR` | 韩语(韩国) | 是 | 是
`mr-IN` | 马拉地语（印度） | 是 | 是
`nb-NO` | 书面挪威语(挪威) | 是 | 否
`nl-NL` | 荷兰语（荷兰） | 是 | 是
`pl-PL` | 波兰语（波兰） | 是 | 否
`pt-BR` | 葡萄牙语（巴西） | 是 | 是
`pt-PT` | 葡萄牙语(葡萄牙) | 是 | 是
`ru-RU` | 俄语（俄罗斯） | 是 | 是
`sv-SE` | 瑞典语（瑞典） | 是 | 否
`ta-IN` | 泰米尔语（印度） | 是 | 是
`te-IN` | 泰卢固语（印度） | 是 | 是
`zh-CN` | 中文(普通话，简体) | 是 | 是
`zh-HK` | 中文（粤语，繁体） | 是 | 是
`zh-TW` | 中文(台湾普通话) | 是 | 是
`th-TH` | 泰语（泰国） | 是 | 否
`tr-TR` | 土耳其 | 是 | 是

## <a name="text-to-speech"></a>文本转语音

Microsoft Speech SDK 和 REST Api 都支持这些声音，其中每个都支持由区域设置标识的特定语言和方言。

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的定价各不相同。 有关其他信息，请访问[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)页。

### <a name="neural-voices"></a>神经语音

神经文本到语音转换是由深度神经网络提供支持的新型语音合成。 使用神经语音时，几乎无法将合成的语音与人类录音区分开来。

可以使用神经语音与聊天机器人和语音助手交互，使其更加自然和吸引人，将电子书等数字文本转换为 audiobooks 并增强汽车内导航系统。 随着类人的自然韵律和字词的清晰发音，用户在与 AI 系统交互时，神经语音显著减轻了听力疲劳。

有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

区域设置 | 语言 | 性别 | 完全服务名称映射 | 短语音名称
--------|----------|--------|---------|------------
`de-DE` | 德语（德国） | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "取消 KatjaNeural"
`en-US` | English (US) | 男 | “Microsoft 服务器语音的文本转语音（en-US，GuyNeural）” | "en-us-GuyNeural"
`en-US` | English (US) | 女 | “Microsoft 服务器语音的文本转语音（en-US，JessaNeural）” | "en-us-JessaNeural"
`it-IT` | 意大利语（意大利） | 女 |"Microsoft Server Speech Text to Speech Voice (it-IT, ElsaNeural)" | "it-ElsaNeural"
`zh-CN` | 中文(中国) | 女 | “Microsoft 服务器语音的文本转语音（zh-CN，XiaoxiaoNeural）” | "zh-CN-XiaoxiaoNeural"

若要了解如何配置和调整神经声音，请参阅[语音合成标记语言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!NOTE]
> 你可以在语音合成请求中使用完整的服务名称映射或短名称。

### <a name="standard-voices"></a>标准语音

提供超过 75 种标准语音，涉及超过 45 种语言和区域设置，允许用户将文本转换为合成语音。 有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

区域设置 | 语言 | 性别 | 完全服务名称映射 | 短名称
-------|----------|---------|----------|----------
<sup>&dagger;</sup>`ar-EG` | 阿拉伯语（埃及） | 女 | “Microsoft 服务器语音的文本转语音（ar-EG，Hoda）” | "ar-EG-Hoda"
`ar-SA` | 阿拉伯语（沙特阿拉伯） | 男 | “Microsoft 服务器语音的文本转语音（ar-SA，Naayf）” | "ar-SA-Naayf"
`bg-BG` | 保加利亚语 | 男 | “Microsoft 服务器语音的文本转语音（bg-BG，Ivan）” | "bg-BG-Ivan"
`ca-ES` | 加泰罗尼亚语(西班牙) | 女 | “Microsoft 服务器语音的文本转语音（ca-ES，HerenaRUS）” | "HerenaRUS"
`cs-CZ` | 捷克语 | 男 | “Microsoft 服务器语音的文本转语音（cs-CZ，Jakub）” | "cs-CZ-Jakub"
`da-DK` | 丹麦语 | 女 | “Microsoft 服务器语音的文本转语音（da-DK，HelleRUS）” | "da-HelleRUS"
`de-AT` | 德语（奥地利） | 男 | “Microsoft 服务器语音的文本转语音（de-AT，Michael）” | "从 Michael 开始"
`de-CH` | 德语（瑞士） | 男 | “Microsoft 服务器语音的文本转语音（de-CH，Karsten）” | "Karsten"
`de-DE` | 德语（德国） | 女 | “Microsoft 服务器语音的文本转语音（de-DE，Hedda）” | "取消 Hedda"
| | | 女 | “Microsoft 服务器语音的文本转语音（de-DE，HeddaRUS）” | "取消 HeddaRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（de-DE，Stefan，Apollo）” | "反 Stefan-Apollo"
`el-GR` | 希腊语 | 男 | “Microsoft 服务器语音的文本转语音（el-GR，Stefanos）” | "萨尔瓦多-Stefanos"
`en-AU` | 英语（澳大利亚） | 女 | “Microsoft 服务器语音的文本转语音（en-AU，Catherine）” | "en-us-Catherine"
| | | 女 | “Microsoft 服务器语音的文本转语音（en-AU，HayleyRUS）” | "en-us-HayleyRUS"
`en-CA` | 英语（加拿大） | 女 | “Microsoft 服务器语音的文本转语音（en-CA，Linda）” | "Linda"
| | | 女 | “Microsoft 服务器语音的文本转语音（en-CA，HeatherRUS）” | "HeatherRUS"
`en-GB` | English (UK) | 女 | “Microsoft 服务器语音的文本转语音（en-GB，Susan，Apollo）” | "en-GB-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（en-GB，HazelRUS）” | "HazelRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（en-GB，George，Apollo）” | "George-Apollo"
`en-IE` | 英语（爱尔兰） | 男 | “Microsoft 服务器语音的文本转语音（en-IE，Sean）” | "en-us-小红"
`en-IN` | English (India) | 女 | “Microsoft 服务器语音的文本转语音（en-IN，Heera，Apollo）” | "Heera-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（en-IN，PriyaRUS）” | "PriyaRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（en-IN，Ravi，Apollo）” | "Ravi-Apollo"
`en-US` | English (US) | 女 | “Microsoft 服务器语音的文本转语音（en-US，ZiraRUS）” | "en-us-ZiraRUS"
| | | 女 | “Microsoft 服务器语音的文本转语音（en-US，JessaRUS）” | "en-US-JessaRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（en-US，BenjaminRUS）” | "en-US-BenjaminRUS"
| | | 女 | “Microsoft 服务器语音的文本转语音（en-US，Jessa24kRUS）” | "en-us-Jessa24kRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（en-US，Guy24kRUS）” | "en-us-Guy24kRUS"
`es-ES` | 西班牙语(西班牙) |女 | “Microsoft 服务器语音的文本转语音（es-ES，Laura，Apollo）” | "es-ES-Laura-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（es-ES，HelenaRUS）” | "es-HelenaRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（es-ES，Pablo，Apollo）” | "es-ES-Pablo-Apollo"
`es-MX` | 西班牙语（墨西哥） | 女 | “Microsoft 服务器语音的文本转语音（es-MX，HildaRUS）” | "HildaRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（es-MX，Raul，Apollo）” | "Raul-Apollo"
`fi-FI` | 芬兰语 | 女 | “Microsoft 服务器语音的文本转语音（fi-FI，HeidiRUS）” | "HeidiRUS"
`fr-CA` | 法语（加拿大） |女 | “Microsoft 服务器语音的文本转语音（fr-CA，Caroline）” | "fr-Caroline"
| | | 女 | “Microsoft 服务器语音的文本转语音（fr-CA，HarmonieRUS）” | "fr-CA-HarmonieRUS"
`fr-CH` | 法语（瑞士）| 男 | “Microsoft 服务器语音的文本转语音（fr-CH，Guillaume）” | "fr-Realytics"
`fr-FR` | 法语（法国）| 女 | “Microsoft 服务器语音的文本转语音（fr-FR，Julie，Apollo）” | "fr-Julie-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（fr-FR，HortenseRUS）” | "fr-FR-HortenseRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（fr-FR，Paul，Apollo）” | "fr-FR-Paul-Apollo"
`he-IL` | 希伯来语（以色列） | 男| “Microsoft 服务器语音的文本转语音（he-IL，Asaf）” | "Asaf"
`hi-IN` | 印地语（印度） | 女 | “Microsoft 服务器语音的文本转语音（hi-IN，Kalpana，Apollo）” | "Kalpana-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（hi-IN，Kalpana）” | "Kalpana"
| | | 男 | “Microsoft 服务器语音的文本转语音（hi-IN，Hemant）” | "Hemant"
`hr-HR` | 克罗地亚语 | 男 | “Microsoft 服务器语音的文本转语音（hr-HR，Matej）” | "hr-Matej"
`hu-HU` | 匈牙利语 | 男 | “Microsoft 服务器语音的文本转语音（hu-HU，Szabolcs）” | "Szabolcs"
`id-ID` | 印度尼西亚语| 男 | “Microsoft 服务器语音的文本转语音（id-ID，Andika）” | "id-ID-Andika"
`it-IT` | 意大利语 | 男 | “Microsoft 服务器语音的文本转语音（it-IT，Cosimo，Apollo）” | "it-IT-Cosimo-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（it-IT，LuciaRUS）” | "it-LuciaRUS"
`ja-JP` | 日语 | 女 | “Microsoft 服务器语音的文本转语音（ja-JP，Ayumi，Apollo）” | "ja-JP-Ayumi-Apollo"
| | | 男 | “Microsoft 服务器语音的文本转语音（ja-JP，Ichiro，Apollo）” | "ja-jp-Ichiro-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（ja-JP，HarukaRUS）” | "ja-jp-HarukaRUS"
`ko-KR` | 韩语 | 女 | “Microsoft 服务器语音的文本转语音（ko-KR，HeamiRUS）” | "HeamiRUS"
`ms-MY` | 马来语 | 男 | “Microsoft 服务器语音的文本转语音（ms-MY，Rizwan）” | "Rizwan"
`nb-NO` | 挪威语 | 女 | “Microsoft 服务器语音的文本转语音（nb-NO，HuldaRUS）” | "nb-HuldaRUS"
`nl-NL` | 荷兰语 | 女 | “Microsoft 服务器语音的文本转语音（nl-NL，HannaRUS）” | "nl-NL-HannaRUS"
`pl-PL` | 波兰语 | 女 | “Microsoft 服务器语音的文本转语音（pl-PL，PaulinaRUS）” | "pl-PL-PaulinaRUS"
`pt-BR` | 葡萄牙语（巴西） | 女 | “Microsoft 服务器语音的文本转语音（pt-BR，HeloisaRUS）” | "pt-BR-HeloisaRUS"
| | | 男 |“Microsoft 服务器语音的文本转语音（pt-BR，Daniel，Apollo）” | "pt-BR-Daniel-Apollo"
`pt-PT` | 葡萄牙语(葡萄牙) | 女 | “Microsoft 服务器语音的文本转语音（pt-PT，HeliaRUS）” | "pt-HeliaRUS"
`ro-RO` | 罗马尼亚语 | 男 | “Microsoft 服务器语音的文本转语音（ro-RO，Andrei）” | "ro-RO-Andrei"
`ru-RU` |俄语| 女 | “Microsoft 服务器语音的文本转语音（ru-RU，Irina，Apollo）” | "ru-RU-Irina-Apollo"
| | | 男 | “Microsoft 服务器语音的文本转语音（ru-RU，Pavel，Apollo）” | "ru-RU-Pavel-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（ru-RU，EkaterinaRUS）” | ru-RU-EkaterinaRUS
`sk-SK` | 斯洛伐克语 | 男 | “Microsoft 服务器语音的文本转语音（sk-SK，Filip）” | "sk-SK-Filip"
`sl-SI` | 斯洛文尼亚语 | 男 | “Microsoft 服务器语音的文本转语音（sl-SI，Lado）” | "sl-Lado"
`sv-SE` | 瑞典语 | 女 | “Microsoft 服务器语音的文本转语音（sv-SE，HedvigRUS）” | "sv-HedvigRUS"
`ta-IN` | 泰米尔语（印度） | 男 | “Microsoft 服务器语音的文本转语音（ta-IN，Valluvar）” | "ta-Valluvar"
`te-IN` | 泰卢固语（印度） | 女 | “Microsoft 服务器语音的文本转语音（te-IN，Chitra）” | "te-Chitra"
`th-TH` | 泰语 | 男 | “Microsoft 服务器语音的文本转语音（th-TH，Pattara）” | "第 Pattara"
`tr-TR` | 土耳其语 | 女 | “Microsoft 服务器语音的文本转语音（tr-TR，SedaRUS）” | "tr-TR-SedaRUS"
`vi-VN` | 越南语 | 男 | “Microsoft 服务器语音的文本转语音（vi-VN，An）” | "vi-VN"
`zh-CN` | 中文(中国) | 女 | “Microsoft 服务器语音的文本转语音（zh-CN，HuihuiRUS）” | "zh-CN-HuihuiRUS"
| | | 女 | “Microsoft 服务器语音的文本转语音（zh-CN，Yaoyao，Apollo）” | "zh-CN-Yaoyao-Apollo"
| | | 男 | “Microsoft 服务器语音的文本转语音（zh-CN，Kangkang，Apollo）” | "zh-CN-Kangkang-Apollo"
`zh-HK` | 中文(香港特别行政区) | 女 | “Microsoft 服务器语音的文本转语音（zh-HK，Tracy，Apollo）” | "zh-CN-Tracy-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（zh-HK，TracyRUS）” | "zh-CN-TracyRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（zh-HK，Danny，Apollo）” | "zh-CN-Danny-Apollo"
`zh-TW` | 中文（台湾） | 女 | “Microsoft 服务器语音的文本转语音（zh-TW，Yating，Apollo）” | "zh-CN-Yating-Apollo"
| | | 女 | “Microsoft 服务器语音的文本转语音（zh-TW，HanHanRUS）” | "zh-TW-HanHanRUS"
| | | 男 | “Microsoft 服务器语音的文本转语音（zh-TW，Zhiwei，Apollo）” | "zh-CN-Zhiwei-Apollo"

&dagger; *ar-例如支持新式标准阿拉伯语（MSA）。*

> [!NOTE]
> 你可以在语音合成请求中使用完整的服务名称映射或短名称。

### <a name="customization"></a>自定义

语音自定义适用于 `de-DE`、`en-GB`、`en-IN`、`en-US`、`es-MX`、`fr-FR`、`it-IT`、`pt-BR`和 `zh-CN`。 选择与训练自定义语音模型所需的定型数据相匹配的正确区域设置。 例如，如果您的记录数据是以英语撰写的，则选择 "`en-GB`"。

> [!NOTE]
> 除了简体中文 bi 语言，我们不支持自定义语音中的 bi 语言模型定型。 如果要训练一种可以说英语的中文语音，请选择 "中文（英语）"。 所有区域设置中的语音训练都以 2000 + 最谈话的数据集开头，除了 `en-US` 和 `zh-CN`，您可以从任何大小的定型数据开始。

## <a name="speech-translation"></a>语音翻译

**语音翻译** API 支持使用不同的语言进行语音转语音和语音转文本的翻译。 源语言必须始终来自语音到文本语言表。 可用的目标语言取决于翻译目标是语音还是文本。 可以将传入的语音翻译成 [60 种以上的语言](https://www.microsoft.com/translator/business/languages/)。 语言子集可用于[语音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文本语言

| 文本语言    | 语言代码 |
|:----------- |:-------------:|
| 南非荷兰语      | `af`          |
| 阿拉伯语       | `ar`          |
| Bangla      | `bn`          |
| 波斯尼亚语(拉丁语系)      | `bs`          |
| 保加利亚语      | `bg`          |
| 粤语(繁体)      | `yue`          |
| 加泰罗尼亚语      | `ca`          |
| 简体中文      | `zh-Hans`          |
| 中文(繁体)      | `zh-Hant`          |
| 克罗地亚语      | `hr`          |
| 捷克语      | `cs`          |
| 丹麦语      | `da`          |
| 荷兰语      | `nl`          |
| 英语      | `en`          |
| 爱沙尼亚语      | `et`          |
| 斐济语      | `fj`          |
| 菲律宾语      | `fil`          |
| 芬兰语      | `fi`          |
| 法语      | `fr`          |
| 德语      | `de`          |
| 希腊语      | `el`          |
| 海地克里奥尔语      | `ht`          |
| 希伯来语      | `he`          |
| Hindi      | `hi`          |
| 白苗文      | `mww`          |
| 匈牙利语      | `hu`          |
| 印度尼西亚语      | `id`          |
| 意大利语      | `it`          |
| 日语      | `ja`          |
| 斯瓦希里语      | `sw`          |
| 克林贡语      | `tlh`          |
| 克林贡语(plqaD)      | `tlh-Qaak`          |
| 韩语      | `ko`          |
| 拉脱维亚语      | `lv`          |
| 立陶宛语      | `lt`          |
| 马达加斯加语      | `mg`          |
| 马来语      | `ms`          |
| 马耳他语      | `mt`          |
| 挪威语      | `nb`          |
| 波斯语      | `fa`          |
| 波兰语      | `pl`          |
| 葡萄牙语      | `pt`          |
| 克雷塔罗奥托米语      | `otq`          |
| 罗马尼亚语      | `ro`          |
| 俄语      | `ru`          |
| 萨摩亚语      | `sm`          |
| 塞尔维亚语（西里尔）      | `sr-Cyrl`          |
| 塞尔维亚语（拉丁）      | `sr-Latn`          |
| 斯洛伐克语     | `sk`          |
| 斯洛文尼亚语      | `sl`          |
| 西班牙语      | `es`          |
| 瑞典语      | `sv`          |
| 塔希提语      | `ty`          |
| 泰米尔语      | `ta`          |
| 泰卢固语      | `te`          |
| 泰语      | `th`          |
| 汤加语      | `to`          |
| 土耳其语      | `tr`          |
| 乌克兰语      | `uk`          |
| 乌尔都语      | `ur`          |
| 越南语      | `vi`          |
| 威尔士语      | `cy`          |
| 尤卡坦玛雅语      | `yua`          |


## <a name="next-steps"></a>后续步骤

* [获取语音服务试用版订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
