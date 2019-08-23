---
title: 语言支持-语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务支持多种语言进行语音到文本和文本到语音转换, 同时提供语音翻译。 本文提供按服务功能列出的语言支持的综合列表。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: abff902dc62ad7ae48f2ecedfbd52aeb96719093
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970373"
---
# <a name="language-and-region-support-for-the-speech-services"></a>语音服务的语言和区域支持

不同的语音服务函数支持不同的语言。 下表汇总了语言支持。

## <a name="speech-to-text"></a>语音转文本

Microsoft speech 识别 SDK 和 REST API 都支持以下语言 (区域设置)。 若要提高准确性, 可通过上传音频和人贴标签的脚本或相关文本, 为语言的子集提供自定义:句子.  发音自定义当前仅适用于 en-us 和 de。 [在此处](how-to-custom-speech.md)了解有关自定义的详细信息。

  代码 | 语言 | 支持 | 可自定义
 ------|----------|---------------------|---------------------
 ar-EG | 阿拉伯语(埃及)，现代标准 | 是 | 是
 ca-ES | 加泰罗尼亚语 | 是 | 否
 da-DK | 丹麦语(丹麦) | 是 | 否
 de-DE | 德语(德国) | 是 | 是
 en-AU | 英语(澳大利亚) | 是 | 是
 en-CA | 英语(加拿大) | 是 | 是
 en-GB | 英语(英国) | 是 | 是
 en-IN | 英语(印度) | 是 | 是
 en-NZ | 英语(新西兰) | 是 | 是
 en-US | 英语(美国) | 是 | 是
 es-ES | 西班牙语(西班牙) | 是 | 是
 es-MX | 西班牙语(墨西哥) | 是 | 是
 fi-FI | 芬兰语(芬兰) | 是 | 否
 fr-CA | 法语(加拿大) | 是 | 是
 fr-FR | 法语(法国) | 是 | 是
 hi-IN | 印地语(印度) | 是 | 是
 it-IT | 意大利语(意大利) | 是 | 是
 ja-JP | 日语(日本) | 是 | 是
 ko-KR | 韩语(韩国) | 是 | 是
 nb-NO | 书面挪威语(挪威) | 是 | 否
 nl-NL | 荷兰语(荷兰) | 是 | 是
 pl-PL | 波兰语(波兰) | 是 | 否
 pt-BR | 葡萄牙语(巴西) | 是 | 是
 pt-PT | 葡萄牙语(葡萄牙) | 是 | 是
 ru-RU | 俄语(俄罗斯) | 是 | 是
 sv-SE | 瑞典语(瑞典) | 是 | 否
 zh-CN | 中文(普通话，简体) | 是 | 是
 zh-HK | 中文 (粤语, 繁体) | 是 | 是
 zh-TW | 中文(台湾普通话) | 是 | 是
 th-TH | 泰语(泰国) | 是 | 否 


## <a name="text-to-speech"></a>文本转语音

文本转语音 REST API 支持以下语音，每种语音支持区域设置标识的特定语言和方言。

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的定价各不相同。 有关其他信息，请访问[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)页。

### <a name="neural-voices"></a>神经语音

神经文本到语音转换是由深度神经网络提供支持的新型语音合成。 使用神经语音时，几乎无法将合成的语音与人类录音区分开来。

可以使用神经语音使与聊天机器人和虚拟助手的交流更加自然和专注、可将数字文本（如电子书）转换为有声读物以及可增强车载导航系统。 随着类人的自然韵律和字词的清晰发音，用户在与 AI 系统交互时，神经语音显著减轻了听力疲劳。

有关神经语音和区域可用性的完整列表，请参阅[区域](regions.md#standard-and-neural-voices)。

区域设置 | 语言 | 性别 | 完全服务名称映射 | 短语音名称
--------|----------|--------|---------|------------
de-DE | 德语(德国) | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "取消 KatjaNeural"
en-US | 英语(美国) | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)" | "en-us-GuyNeural"
en-US | 英语(美国) | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)" | "en-us-JessaNeural"
it-IT | 意大利语(意大利) | 女 |"Microsoft Server Speech Text to Speech Voice (it-IT, ElsaNeural)" | "it-ElsaNeural"
zh-CN | 中文(中国) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-chs-XiaoxiaoNeural"

> [!NOTE]
> 你可以在语音合成请求中使用完整的服务名称映射或短名称。

### <a name="standard-voices"></a>标准语音

提供超过 75 种标准语音，涉及超过 45 种语言和区域设置，允许用户将文本转换为合成语音。 有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

区域设置 | 语言 | 性别 | 完全服务名称映射 | 短语音名称
-------|----------|---------|----------|----------
ar-EG\* | 阿拉伯语(埃及) | 女 | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)" | "ar-EG-Hoda"
ar-SA | 阿拉伯语(沙特阿拉伯) | 男 | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | 保加利亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-Ivan"
ca-ES | 加泰罗尼亚语(西班牙) | 女 | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)" | "HerenaRUS"
cs-CZ | 捷克语 | 男 | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub"
da-DK | 丹麦语 | 女 | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-HelleRUS"
de-AT | 德语(奥地利) | 男 | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "从 Michael 开始"
de-CH | 德语(瑞士) | 男 | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)" | "Karsten"
de-DE | 德语(德国) | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "取消 Hedda"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "取消 HeddaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "反 Stefan-Apollo"
el-GR | 希腊语 | 男 | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "萨尔瓦多-Stefanos"
en-AU | 英语(澳大利亚) | 女 | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)" | "en-us-Catherine"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)" | "en-us-HayleyRUS"
en-CA | 英语(加拿大) | 女 | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)" | "Linda"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)" | "HeatherRUS"
en-GB | 英语(英国) | 女 | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)" | "HazelRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)" | "George-Apollo"
en-IE | 英语(爱尔兰) | 男 | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)" | "en-us-小红"
en-IN | 英语(印度) | 女 | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)" | "Heera-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)" | "PriyaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)" | "Ravi-Apollo"
en-US | 英语(美国) | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)" | "en-us-ZiraRUS"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" | "en-us-Jessa24kRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)" | "en-us-Guy24kRUS"
es-ES | 西班牙语(西班牙) |女 | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)" | "es-HelenaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | 西班牙语(墨西哥) | 女 | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "HildaRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)" | "Raul-Apollo"
fi-FI | 芬兰语 | 女 | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)" | "HeidiRUS"
fr-CA | 法语(加拿大) |女 | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)" | "fr-Caroline"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
fr-CH | 法语(瑞士)| 男 | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)" | "fr-Realytics"
fr-FR | 法语(法国)| 女 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-Julie-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| 希伯来语(以色列) | 男| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)" | "Asaf"
hi-IN | 印地语(印度) | 女 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)" | "Kalpana-Apollo"
| | |女 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)" | "Kalpana"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)" | "Hemant"
hr-HR | 克罗地亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)" | "hr-Matej"
hu-HU | 匈牙利语 | 男 | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "Szabolcs"
id-ID | 印度尼西亚语| 男 | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)" | "id-ID-Andika"
it-IT | 意大利语 | 男 | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)" | "it-LuciaRUS"
ja-JP | 日语 | 女 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-jp-Ichiro-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)" | "ja-jp-HarukaRUS"
ko-KR | 韩语 | 女 | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "HeamiRUS"
ms-MY | 马来语 | 男 | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "Rizwan"
nb-NO | 挪威语 | 女 | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)" | "nb-HuldaRUS"
nl-NL | 荷兰语 | 女 | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | 波兰语 | 女 | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | 葡萄牙语(巴西) | 女 | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | 男 |"Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | 葡萄牙语(葡萄牙) | 女 | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-HeliaRUS"
ro-RO | 罗马尼亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei"
ru-RU |俄语| 女 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk-SK | 斯洛伐克语 | 男 | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip"
sl-SI | 斯洛文尼亚语 | 男 | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)" | "sl-Lado"
sv-SE | 瑞典语 | 女 | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)" | "sv-HedvigRUS"
ta-IN | 泰米尔语(印度) | 男 | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)" | "ta-Valluvar"
te-IN | 泰卢固语(印度) | 女 | "Microsoft Server Speech Text to Speech Voice (te-IN, Chitra)" | "te-Chitra"
th-TH | 泰语 | 男 | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "第 Pattara"
tr-TR | 土耳其语 | 女 | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | 越南语 | 男 | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)" | "vi-VN"
zh-CN | 中文(中国) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-chs-HuihuiRUS"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-chs-Kangkang-Apollo"
zh-HK | 中文(香港特别行政区) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-chs-Tracy-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)" | "zh-chs-TracyRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-chs-Danny-Apollo"
zh-TW | 中文(中国台湾) | 女 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-chs-Yating-Apollo"
| | | 女 | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | 男 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-chs-Zhiwei-Apollo"

\* *ar-EG 支持现代标准阿拉伯语 (MSA)。*

> [!NOTE]
> 你可以在语音合成请求中使用完整的服务名称映射或短名称。

### <a name="customization"></a>自定义

语音自定义适用于 de、en、en、en-us、en-us、es-mx、fr、it、zh-chs、pt-BR 和-CN。 选择与训练自定义语音模型所需的定型数据相匹配的正确区域设置。 例如, 如果您的记录数据是以英语撰写的, 则选择 "en-us"。  

> [!NOTE]
> 除了简体中文 bi 语言, 我们不支持自定义语音中的 bi 语言模型定型。 如果要训练一种可以说英语的中文语音, 请选择 "中文-英语双语"。 所有区域设置中的语音训练都以 2000 + 最谈话的数据集开头, 除了 en-us 和 zh-chs, 你可以从任何大小的定型数据开始。

## <a name="speech-translation"></a>语音翻译

**语音翻译** API 支持使用不同的语言进行语音转语音和语音转文本的翻译。 源语言必须始终来自“语音转文本”语言表。 可用的目标语言取决于翻译目标是语音还是文本。 可以将传入的语音翻译成 [60 种以上的语言](https://www.microsoft.com/translator/business/languages/)。 这些语言的子集可用于[语音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文本语言

| 文本语言    | 语言代码 |
|:----------- |:-------------:|
| 南非荷兰语      | `af`          |
| 阿拉伯语       | `ar`          |
| 孟加拉语      | `bn`          |
| 波斯尼亚语(拉丁语系)      | `bs`          |
| 保加利亚语      | `bg`          |
| 粤语(繁体)      | `yue`          |
| 加泰罗尼亚语      | `ca`          |
| 简体中文      | `zh-Hans`          |
| 繁体中文      | `zh-Hant`          |
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
| 印地语      | `hi`          |
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
| 马拉加斯语      | `mg`          |
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
| 塞尔维亚语(西里尔文)      | `sr-Cyrl`          |
| 塞尔维亚语(拉丁语系)      | `sr-Latn`          |
| 斯洛伐克语     | `sk`          |
| 斯洛文尼亚语      | `sl`          |
| 西班牙语      | `es`          |
| 瑞典语      | `sv`          |
| 塔希提语      | `ty`          |
| 泰米尔语      | `ta`          |
| 泰语      | `th`          |
| 汤加语      | `to`          |
| 土耳其语      | `tr`          |
| 乌克兰语      | `uk`          |
| 乌尔都语      | `ur`          |
| 越南语      | `vi`          |
| 威尔士语      | `cy`          |
| 尤卡坦玛雅语      | `yua`          |


## <a name="next-steps"></a>后续步骤

* [获取语音服务试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
