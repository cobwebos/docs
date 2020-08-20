---
title: 语言支持-翻译人员
titleSuffix: Azure Cognitive Services
description: 认知服务转换器支持使用神经机器翻译 (NMT) 进行文本到文本转换的以下语言。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: swmachan
ms.openlocfilehash: cc5964bb243216233300e8616b0460b7a1a043d9
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661531"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>文本和语音翻译的语言和区域支持

使用转换器转换为70多个文本翻译语言的任何语言。 神经机器翻译 (NMT) 是高质量 AI 计算机翻译的新标准，可在神经系统可用时使用转换器的 V3 作为默认值。

你还可以结合使用转换器与自定义转换器来构建神经翻译系统，以了解你自己的业务和行业中使用的术语，并使用 Microsoft Speech Service 将语音翻译添加到应用中。

[详细了解机器翻译的工作原理](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>文本翻译
可以使用转换操作对翻译人员提供的任何语言进行文本转换。 该 API 还通过使用检测操作、音译和直译操作提供语言检测，并使用字典查找和字典示例操作提供双语词典。 下面列出了每个操作的可用语言。 

### <a name="translate"></a>Translate

翻译人员支持文本转换的以下语言。 

[查看翻译操作参考文档](reference/v3-0-translate.md)

|语言|  语言代码|
|:-----|:-----:|
|南非荷兰语| `af`|
|阿拉伯语|    `ar`    |
|Bangla|    `bn`    |
|波斯尼亚语(拉丁语系)|   `bs`    |
|保加利亚语| `bg`    |
|粤语(繁体)|   `yue`|
|加泰罗尼亚语|   `ca`    |
|简体中文|    `zh-Hans`|
|中文(繁体)|   `zh-Hant`       |
|克罗地亚语|  `hr`    |
|捷克语| `cs`    |
|达里语|  `prs`   |
|丹麦语|    `da`        |
|荷兰语| `nl`|
|英语|   `en`    |
|爱沙尼亚语|  `et`    |
|斐济语|    `fj`    |
|菲律宾语|  `fil`   |
|芬兰语|   `fi`    |
|法语|    `fr`    |
|德语|    `de`    |
|希腊语| `el`    |
|古吉拉特语|  `gu`    |
|海地克里奥尔语|    `ht`        |
|希伯来语 |`he`   |
|Hindi| `hi`    |
|白苗文| `mww`   |
|匈牙利语| `hu`    |
|冰岛语| `is`    |
|印度尼西亚语|    `id`    |
|爱尔兰语 | `ga`|
|意大利语|   `it`    |
|日语|  `ja`    |
|卡纳达语|`kn`|
|哈萨克语|`kk`|
|斯瓦希里语| `sw`    |
|克林贡语|   `tlh-Latn`  |
|克林贡语(plqaD)|   `tlh-Piqd`  |
|韩语 |`ko`   |
|库尔德语 (中部)   |`ku`   |
|北) 库尔德语 ( |`kmr`  |
|拉脱维亚语|   `lv`    |
|立陶宛语|    `lt`    |
|马达加斯加语|  `mg`    |
|马来语| `ms`        |
|马拉雅拉姆语| `ml` |
|马耳他语|   `mt`    |
|毛利语| `mi`  |
|马拉地语| `mr`  |
|挪威语| `nb`    |
|奥里亚语|  `or`    |
|普什图语|    `ps`    |
|波斯语|   `fa`    |
|波兰语|    `pl`    |
|葡萄牙语（巴西）|   `pt-br` |
|葡萄牙语(葡萄牙)| `pt-pt` |
|旁遮普语|`pa`|
|克雷塔罗奥托米语|   `otq`   |
|罗马尼亚语|  `ro`    |
|俄语|   `ru`    |
|萨摩亚语|    `sm`    |
|塞尔维亚语（西里尔）|    `sr-Cyrl`|
|塞尔维亚语（拉丁）|   `sr-Latn`       |
|斯洛伐克语|    `sk`    |
|斯洛文尼亚语| `sl`    |
|西班牙语|   `es`    |
|瑞典语|   `sv`    |
|塔希提语|  `ty`    |
|泰米尔语| `ta`    |
|泰卢固语|    `te`    |
|泰语|  `th`    |
|汤加语|    `to`    |
|土耳其语|   `tr`        |
|乌克兰语| `uk`    |
|乌尔都语|  `ur`    |
|越南语|    `vi`    |
|威尔士语| `cy`    |
|尤卡坦玛雅语|  `yua`   |

> [!NOTE]
> 语言代码 `pt` 将默认为 `pt-br` ，葡萄牙 (巴西) 。

### <a name="detect"></a>Detect

转换器检测到以下用于翻译和音译的语言。

[查看检测操作参考文档](reference/v3-0-detect.md)

|语言|  语言代码|
|:-----|:-----:|
|南非荷兰语| `af`|
|阿拉伯语|    `ar`    |
|保加利亚语| `bg`    |
|加泰罗尼亚语|   `ca`    |
|简体中文|    `zh-Hans`|
|中文(繁体)|   `zh-Hant`       |
|克罗地亚语|  `hr`    |
|捷克语| `cs`    |
|丹麦语|    `da`        |
|荷兰语| `nl`|
|英语|   `en`    |
|爱沙尼亚语|  `et`    |
|芬兰语|   `fi`    |
|法语|    `fr`    |
|德语|    `de`    |
|希腊语| `el`    |
|古吉拉特语|  `gu`    |
|海地克里奥尔语|    `ht`        |
|希伯来语 |`he`   |
|Hindi| `hi`    |
|匈牙利语| `hu`    |
|冰岛语| `is`    |
|印度尼西亚语|    `id`    |
|爱尔兰语 | `ga`|
|意大利语|   `it`    |
|日语|  `ja`    |
|斯瓦希里语| `sw`    |
|克林贡语|   `tlh-Latn`  |
|韩语 |`ko`   |
|拉脱维亚语|   `lv`    |
|立陶宛语|    `lt`    |
|马来语| `ms`        |
|马耳他语|   `mt`    |
|挪威语| `nb`    |
|波斯语|   `fa`    |
|波兰语|    `pl`    |
|葡萄牙语（巴西）|   `pt-br` |
|葡萄牙语(葡萄牙)| `pt-pt` |
|罗马尼亚语|  `ro`    |
|俄语|   `ru`    |
|塞尔维亚语（西里尔）|    `sr-Cyrl`|
|塞尔维亚语（拉丁）|   `sr-Latn`       |
|斯洛伐克语|    `sk`    |
|斯洛文尼亚语| `sl`    |
|西班牙语|   `es`    |
|瑞典语|   `sv`    |
|塔希提语|  `ty`    |
|泰语|  `th`    |
|土耳其语|   `tr`        |
|乌克兰语| `uk`    |
|乌尔都语|  `ur`    |
|越南语|    `vi`    |
|威尔士语| `cy`    |
|尤卡坦玛雅语|  `yua`   |

### <a name="transliterate"></a>Transliterate

Transliterate 方法支持以下语言。 在“音译方向”中，“<-->”表示该语言可以从列出的两侧脚本互相音译。 “-->” 表示该语言只能从一个脚本音译到另一个脚本。

[查看直译操作参考文档](reference/v3-0-translate.md)


| 语言    | 语言代码 | 脚本 | 到/从 | 脚本|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| 阿拉伯语 | `ar` | 阿拉伯语 `Arab` | <--> | 拉丁语 `Latn` |
| Bangla  | `bn` | 孟加拉语 `Beng` | <--> | 拉丁语 `Latn` |
| 中文（简体） | `zh-Hans` | 简体中文 `Hans`| <--> | 拉丁语 `Latn` |
| 中文（简体） | `zh-Hans` | 简体中文 `Hans`| <--> | 繁体中文 `Hant`|
| 和 SharePoint 2010 显示的“中文(繁体)” | `zh-Hant` | 繁体中文 `Hant`| <--> | 拉丁语 `Latn` |
| 和 SharePoint 2010 显示的“中文(繁体)” | `zh-Hant` | 繁体中文 `Hant`| <--> | 简体中文 `Hans` |
| 古吉拉特语 | `gu`  | 古吉拉特语 `Gujr` | --> | 拉丁语 `Latn` |
| 希伯来语 | `he` | 希伯来语 `Hebr` | <--> | 拉丁语 `Latn` |
| Hindi | `hi` | 梵语 `Deva` | <--> | 拉丁语 `Latn` |
| 日语 | `ja` | 日语 `Jpan` | <--> | 拉丁语 `Latn` |
| 卡纳达语 | `kn` | 卡纳达语 `Knda` | --> | 拉丁语 `Latn` |
| 马拉雅拉姆语 | `ml` | 马拉雅拉姆语 `Mlym` | --> | 拉丁语 `Latn` |
| 马拉地语 | `mr` | 梵语 `Deva` | --> | 拉丁语 `Latn` |
| 奥里亚语 | `or` | 奥里雅语 `Orya` | <--> | 拉丁语 `Latn` |
| 旁遮普语 | `pa` | 果鲁穆奇语 `Guru`  | <--> | 拉丁语 `Latn`  |
| 塞尔维亚语（西里尔） | `sr-Cyrl` | 西里尔语 `Cyrl`  | --> | 拉丁语 `Latn` |
| 塞尔维亚语（拉丁） | `sr-Latn` | 拉丁语 `Latn` | --> | 西里尔语 `Cyrl`|
| 泰米尔语 | `ta` | 泰米尔语 `Taml` | --> | 拉丁语 `Latn` |
| 泰卢固语 | `te` | 泰卢固语 `Telu` | --> | 拉丁语 `Latn` |
| 泰语 | `th` | 泰语 `Thai` | --> | 拉丁语 `Latn` |

### <a name="dictionary"></a>字典

字典使用 Lookup 和 Examples 方法支持以下将语言翻译成英语或将英语翻译为以下语言。

查看 [字典查找](reference/v3-0-dictionary-lookup.md) 和 [字典示例](reference/v3-0-dictionary-examples.md) 操作的参考文档。

| 语言    | 语言代码 |
|:----------- |:-------------:|
| 南非荷兰语      | `af`          |
| 阿拉伯语       | `ar`          |
| Bangla      | `bn`          |
| 波斯尼亚语(拉丁语系)      | `bs`          |
| 保加利亚语      | `bg`          |
| 加泰罗尼亚语      | `ca`          |
| 简体中文      | `zh-Hans`          |
| 克罗地亚语      | `hr`          |
| 捷克语      | `cs`          |
| 丹麦语      | `da`          |
| 荷兰语      | `nl`          |
| 爱沙尼亚语      | `et`          |
| 芬兰语      | `fi`          |
| 法语      | `fr`          |
| 德语      | `de`          |
| 希腊语      | `el`          |
| 海地克里奥尔语      | `ht`          |
| 希伯来语      | `he`          |
| Hindi      | `hi`          |
| 白苗文      | `mww`          |
| 匈牙利语      | `hu`          |
| 冰岛语    | `is`  |
| 印度尼西亚语      | `id`          |
| 意大利语      | `it`          |
| 日语      | `ja`          |
| 斯瓦希里语      | `sw`          |
| 克林贡语      | `tlh`          |
| 韩语      | `ko`          |
| 拉脱维亚语      | `lv`          |
| 立陶宛语      | `lt`          |
| 马来语      | `ms`          |
| 马耳他语      | `mt`          |
| 挪威语      | `nb`          |
| 波斯语      | `fa`          |
| 波兰语      | `pl`          |
| 葡萄牙语(巴西)     | `pt-br`          |
| 罗马尼亚语      | `ro`          |
| 俄语      | `ru`          |
| 塞尔维亚语（拉丁）      | `sr-Latn`          |
| 斯洛伐克语     | `sk`          |
| 斯洛文尼亚语      | `sl`          |
| 西班牙语      | `es`          |
| 瑞典语      | `sv`          |
| 泰米尔语      | `ta`          |
| 泰语      | `th`          |
| 土耳其语      | `tr`          |
| 乌克兰语      | `uk`          |
| 乌尔都语      | `ur`          |
| 越南语      | `vi`          |
| 威尔士语      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>以编程方式访问转换器语言列表

您可以使用语言方法检索支持的语言的列表。 可按功能、语言代码以及该语言在英文或任何其他受支持语言中的名称来查看列表。 当有新语言可用时，Microsoft Translator 服务会自动更新此列表。

[查看语言操作参考文档](reference/v3-0-languages.md)

## <a name="customization"></a>自定义

以下语言可供使用 [自定义转换器](https://aka.ms/CustomTranslator)的自定义或从英语进行自定义。

| 语言    | 语言代码 |
|:----------- |:-------------:|
| 阿拉伯语       | `ar`          |
| Bangla      | `bn`          |
| 波斯尼亚语(拉丁语系)      | `bs`          |
| 保加利亚语      | `bg`          |
| 简体中文      | `zh-Hans`          |
|中文(繁体)|   `zh-Hant`   |
| 克罗地亚语      | `hr`          |
| 捷克语      | `cs`          |
| 丹麦语      | `da`          |
| 荷兰语      | `nl`          |
| 英语    | `en`     |
| 爱沙尼亚语      | `et`          |
| 芬兰语      | `fi`          |
| 法语      | `fr`          |
| 德语      | `de`          |
| 希腊语      | `el`          |
| 古吉拉特语| `gu`    |
| 希伯来语      | `he`          |
| Hindi      | `hi`          |
| 匈牙利语      | `hu`          |
| 冰岛语 | `is` |
| 印度尼西亚语|   `id`    |
| 爱尔兰语 | `ga`  |
| 意大利语      | `it`          |
| 日语      | `ja`          |
| 斯瓦希里语|    `sw`    |
| 韩语      | `ko`          |
| 拉脱维亚语      | `lv`          |
| 立陶宛语      | `lt`          |
| 马达加斯加语| `mg`    |
| 马来语|    `ms`        |
| 毛利语| `mi`  |
| 马拉地语| `mr`  |
| 挪威语      | `nb`          |
| 波斯语      | `fa`          |
| 波兰语      | `pl`          |
| 葡萄牙语(巴西) | `pt-br` |
| 旁遮普语|`pa`|
| 罗马尼亚语      | `ro`          |
| 俄语      | `ru`          |
| 萨摩亚语|   `sm`    |
| 塞尔维亚语（拉丁）      | `sr-Latn`          |
| 斯洛伐克语     | `sk`          |
| 斯洛文尼亚语      | `sl`          |
| 西班牙语      | `es`          |
| 瑞典语      | `sv`          |
| 泰语      | `th`          |
| 土耳其语      | `tr`          |
| 乌克兰语      | `uk`          |
| 乌尔都语| `ur`    |
| 越南语      | `vi`          |
| 威尔士语 | `cy` |

## <a name="speech-translation"></a>语音翻译
语音翻译可通过使用带有认知服务语音服务的转换器获得。 查看 [语音服务文档](https://docs.microsoft.com/azure/cognitive-services/speech-service/) ，了解有关使用语音翻译和查看所有 [可用语言选项](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)的详细信息。

### <a name="speech-to-text"></a>语音转文本
将语音转换为文本，以便转换为所选的文本语言。 语音转换文本用于语音转换文本，或在与语音合成一起使用时用于语音到语音转换。

| 语言    |
|:----------- |
|阿拉伯语|
|粤语(繁体)|
|加泰罗尼亚语|
|简体中文|
|中文(繁体)|
|丹麦语|
|荷兰语|
|英语|
|芬兰语|
|法语|
|德语|
|古吉拉特语|
|Hindi|
|意大利语|
|日语|
|朝鲜语|
|马拉地语|
|挪威语|
|波兰语|
|葡萄牙语（巴西）|
|葡萄牙语(葡萄牙)|
|俄语|
|西班牙语|
|瑞典语|
|泰米尔语|
|泰卢固语|
|泰语|
|土耳其语|

### <a name="text-to-speech"></a>文本转语音
将文本转换为语音。 使用文本到语音功能可以在使用语音到文本时添加语音转换结果的音频输出，或用于语音语音转换。 

| 语言    |
|:----------- |
|阿拉伯语|
|保加利亚语|
|粤语(繁体)|
|加泰罗尼亚语|
|简体中文|
|中文(繁体)|
|克罗地亚语|
|捷克语|
|丹麦语|
|荷兰语|
|英语|
|芬兰语|
|法语|
|德语|
|希腊语|
|希伯来语|
|Hindi|
|匈牙利语|
|印度尼西亚语|
|意大利语|
|日语|
|朝鲜语|
|马来语|
|挪威语|
|波兰语|
|葡萄牙语（巴西）|
|葡萄牙语(葡萄牙)|
|罗马尼亚语|
|俄语|
|斯洛伐克语|
|斯洛文尼亚语|
|西班牙语|
|瑞典语|
|泰米尔语|
|泰卢固语|
|泰语|
|土耳其语|
|越南语|

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>查看 Microsoft Translator 网站上的语言列表

若要快速查看这些语言，Microsoft Translator 网站将显示翻译人员提供的用于转换文本翻译和语音服务的所有语言。 此列表不包括特定于开发者的信息，例如语言代码。

[查看语言列表](https://www.microsoft.com/translator/languages.aspx)
