---
title: 语音服务支持的语言 - Microsoft 认知服务
description: 语音服务支持的语言列表。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2a768846958f73ace22971f2f2ca8f886c9df252
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282150"
---
# <a name="supported-languages-for-speech-service"></a>语音服务支持的语言

不同的语音服务函数支持不同的语言。 下表汇总了语言支持。

## <a name="speech-to-text"></a>语音转文本

Microsoft 语音识别 API 支持以下语言。 

  代码 | 语言 | 声学适应 | 语言适应 | 发音适应
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | 阿拉伯语(埃及)，现代标准 | 否 | 是 | 否
 it-IT | 意大利语(意大利) | 否 | 是 | 否 
 de-DE | 德语(德国) | 否 | 是 | 是
 ja-JP | 日语(日本) | 否 | 是 | 否
 en-US | 英语(美国) | 是 | 是 | 是
 pt-BR | 葡萄牙语(巴西) | 否 | 是 | 否
 es-ES | 西班牙语(西班牙) | 否 | 是 | 否
 ru-RU | 俄语(俄罗斯) | 否 | 是 | 否
 fr-FR | 法语(法国) | 否 | 是 | 否
 zh-CN | 中文(普通话，简体) | 否 | 是 | 否

## <a name="text-to-speech"></a>文本到语音转换

**文本转语音** API 提供以下语音，每种语音支持区域设置标识的特定语言和方言。

区域设置 | 语言 | 性别 | 服务名称映射
-------|----------|---------|--------------------
ar-EG* | 阿拉伯语(埃及) | 女 | “Microsoft 服务器语音的文本转语音（ar-EG，Hoda）”
ar-SA | 阿拉伯语(沙特阿拉伯) | 男 | “Microsoft 服务器语音的文本转语音（ar-SA，Naayf）”
bg-BG | 保加利亚语 | 男 | “Microsoft 服务器语音的文本转语音（bg-BG，Ivan）”
ca-ES | 加泰罗尼亚语(西班牙) | 女 | “Microsoft 服务器语音的文本转语音（ca-ES，HerenaRUS）”
cs-CZ | 捷克语 | 男 | “Microsoft 服务器语音的文本转语音（cs-CZ，Jakub）”
da-DK | 丹麦语 | 女 | “Microsoft 服务器语音的文本转语音（da-DK，HelleRUS）”
de-AT | 德语(奥地利) | 男 | “Microsoft 服务器语音的文本转语音（de-AT，Michael）”
de-CH | 德语(瑞士) | 男 | “Microsoft 服务器语音的文本转语音（de-CH，Karsten）”
de-DE | 德语(德国) | 女 | “Microsoft 服务器语音的文本转语音（de-DE，Hedda）”
| | | 女 | “Microsoft 服务器语音的文本转语音（de-DE，HeddaRUS）”
| | | 男 | “Microsoft 服务器语音的文本转语音（de-DE，Stefan，Apollo）”
el-GR | 希腊语 | 男 | “Microsoft 服务器语音的文本转语音（el-GR，Stefanos）”
en-AU | 英语(澳大利亚) | 女 | “Microsoft 服务器语音的文本转语音（en-AU，Catherine）”
| | | 女 | “Microsoft 服务器语音的文本转语音（en-AU，HayleyRUS）”
en-CA | 英语(加拿大) | 女 | “Microsoft 服务器语音的文本转语音（en-CA，Linda）”
| | | 女 | “Microsoft 服务器语音的文本转语音（en-CA，HeatherRUS）”
en-GB | 英语(英国) | 女 | “Microsoft 服务器语音的文本转语音（en-GB，Susan，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（en-GB，HazelRUS）”
| | |男 | “Microsoft 服务器语音的文本转语音（en-GB，George，Apollo）”
en-IE | 英语(爱尔兰) |男 | “Microsoft 服务器语音的文本转语音（en-IE，Sean）”
en-IN | 英语(印度) | 女 | “Microsoft 服务器语音的文本转语音（en-IN，Heera，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（en-IN，PriyaRUS）”
| | |男 | “Microsoft 服务器语音的文本转语音（en-IN，Ravi，Apollo）”
en-US | 英语(美国) |女 | “Microsoft 服务器语音的文本转语音（en-US，ZiraRUS）”
| | |女 | “Microsoft 服务器语音的文本转语音（en-US，JessaRUS）”
| | |男 | “Microsoft 服务器语音的文本转语音（en-US，BenjaminRUS）”
| | |女 | “Microsoft 服务器语音的文本转语音（en-US，Jessa24kRUS）” 
| | |男 | “Microsoft 服务器语音的文本转语音（en-US，Guy24kRUS）”
es-ES | 西班牙语(西班牙) |女 | “Microsoft 服务器语音的文本转语音（es-ES，Laura，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（es-ES，HelenaRUS）”
| | |男 | “Microsoft 服务器语音的文本转语音（es-ES，Pablo，Apollo）”
es-MX | 西班牙语(墨西哥) | 女 | “Microsoft 服务器语音的文本转语音（es-MX，HildaRUS）”
| | | 男 | “Microsoft 服务器语音的文本转语音（es-MX，Raul，Apollo）”
fi-FI | 芬兰语 | 女 | “Microsoft 服务器语音的文本转语音（fi-FI，HeidiRUS）”
fr-CA | 法语(加拿大) |女 | “Microsoft 服务器语音的文本转语音（fr-CA，Caroline）”
| | | 女 | “Microsoft 服务器语音的文本转语音（fr-CA，HarmonieRUS）”
fr-CH | 法语(瑞士)|男 | “Microsoft 服务器语音的文本转语音（fr-CH，Guillaume）”
fr-FR | 法语(法国)|女 | “Microsoft 服务器语音的文本转语音（fr-FR，Julie，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（fr-FR，HortenseRUS）”
| | |男 | “Microsoft 服务器语音的文本转语音（fr-FR，Paul，Apollo）”
he-IL| 希伯来语(以色列) | 男| “Microsoft 服务器语音的文本转语音（he-IL，Asaf）”
hi-IN | 印地语(印度) | 女 | “Microsoft 服务器语音的文本转语音（hi-IN，Kalpana，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（hi-IN，Kalpana）”
| | | 男 | “Microsoft 服务器语音的文本转语音（hi-IN，Hemant）”
hr-HR | 克罗地亚语 | 男 | “Microsoft 服务器语音的文本转语音（hr-HR，Matej）”
hu-HU | 匈牙利语 | 男 | “Microsoft 服务器语音的文本转语音（hu-HU，Szabolcs）”
id-ID | 印度尼西亚语| 男 | “Microsoft 服务器语音的文本转语音（id-ID，Andika）”
it-IT | 意大利语 |男 | “Microsoft 服务器语音的文本转语音（it-IT，Cosimo，Apoll）”
| | |女 | “Microsoft 服务器语音的文本转语音（it-IT，LuciaRUS）”
ja-JP | 日语 |女 | “Microsoft 服务器语音的文本转语音（ja-JP，Ayumi，Apollo）”
| | |男 | “Microsoft 服务器语音的文本转语音（ja-JP，Ichiro，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（ja-JP，HarukaRUS）”
ko-KR | 韩语 |女 | “Microsoft 服务器语音的文本转语音（ko-KR，HeamiRUS）”
ms-MY | 马来语|男 | “Microsoft 服务器语音的文本转语音（ms-MY，Rizwan）”
nb-NO | 挪威语|女 | “Microsoft 服务器语音的文本转语音（nb-NO，HuldaRUS）”
nl-NL | 荷兰语|女 | “Microsoft 服务器语音的文本转语音（nl-NL，HannaRUS）”
pl-PL | 波兰语|女 | “Microsoft 服务器语音的文本转语音（pl-PL，PaulinaRUS）”
pt-BR | 葡萄牙语(巴西)|女 | “Microsoft 服务器语音的文本转语音（pt-BR，HeloisaRUS）”
| | |男 | “Microsoft 服务器语音的文本转语音（pt-BR，Daniel，Apollo）”
pt-PT | 葡萄牙语(葡萄牙)|女 | “Microsoft 服务器语音的文本转语音（pt-PT，HeliaRUS）”
ro-RO | 罗马尼亚语|男 | “Microsoft 服务器语音的文本转语音（ro-RO，Andrei）”
ru-RU |俄语| 女 | “Microsoft 服务器语音的文本转语音（ru-RU，Irina，Apollo）”
| | |男 | “Microsoft 服务器语音的文本转语音（ru-RU，Pavel，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（ru-RU，EkaterinaRUS）”
sk-SK | 斯洛伐克语|男 | “Microsoft 服务器语音的文本转语音（sk-SK，Filip）”
sl-SI | 斯洛文尼亚语|男 | “Microsoft 服务器语音的文本转语音（sl-SI，Lado）”
sv-SE | 瑞典语|女 | “Microsoft 服务器语音的文本转语音（sv-SE，HedvigRUS）”
ta-IN | 泰米尔语(印度) |男 | “Microsoft 服务器语音的文本转语音（ta-IN，Valluvar）”
th-TH | 泰语|男 | “Microsoft 服务器语音的文本转语音（th-TH，Pattara）”
tr-TR |土耳其语| 女 | “Microsoft 服务器语音的文本转语音（tr-TR，SedaRUS）”
vi-VN | 越南语|男 | “Microsoft 服务器语音的文本转语音（vi-VN，An）”
zh-CN | 中文(中国大陆)|女 | “Microsoft 服务器语音的文本转语音（zh-CN，HuihuiRUS）”
| | |女 | “Microsoft 服务器语音的文本转语音（zh-CN，Yaoyao，Apollo）”
| | |男 | “Microsoft 服务器语音的文本转语音（zh-CN，Kangkang，Apollo）”
zh-HK | 中文(香港特别行政区)|女 | “Microsoft 服务器语音的文本转语音（zh-HK，Tracy，Apollo）”
| | |女 | “Microsoft 服务器语音的文本转语音（zh-HK，TracyRUS）”
| || 男 | “Microsoft 服务器语音的文本转语音（zh-HK，Danny，Apollo）”
zh-TW | 中文(中国台湾)|女 | “Microsoft 服务器语音的文本转语音（zh-TW，Yating，Apollo）”
| || 女 | “Microsoft 服务器语音的文本转语音（zh-TW，HanHanRUS）”
| || 男 | “Microsoft 服务器语音的文本转语音（zh-TW，Zhiwei，Apollo）”

\* *ar-EG 支持现代标准阿拉伯语 (MSA)。*

### <a name="customization"></a>自定义

自定义**文本转语音**语音字体支持美国英语 (en-US) 和中文 (zh-CN)。

## <a name="speech-translation"></a>语音翻译

**语音翻译** API 支持使用不同的语言进行语音转语音和语音转文本的翻译。 源语言必须始终来自以下“语音语言”表。 可用的目标语言取决于翻译目标是语音还是文本。

可以编程方式或使用 REST API 的“语言”终结点获取语言列表。 此列表提供了每个语言代码，并以英语或任何其他受支持语言提供语言名称。 每当支持新的语言时，Microsoft Translator 服务都会更新此列表。

### <a name="speech-languages"></a>语音语言

| 语音语言   | 语言代码 |
|:----------- |-|
| 阿拉伯语(现代标准)      | `ar` |
| 中文(普通话)      | `zh` |
| 英语      | `en` |
| 法语      | `fr` |
| 德语      | `de` |
| 意大利语      | `it` |
| 日语      | `jp` |
| 葡萄牙语(巴西)     | `pt` |
| 俄语      | `ru` |
| 西班牙语      |  `es` |

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
| 中文(简体)      | `zh-Hans`          | 
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

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
