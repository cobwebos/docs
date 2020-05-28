---
title: 语言支持-翻译人员
titleSuffix: Azure Cognitive Services
description: 认知服务转换器支持使用神经机器翻译（NMT）转换文本到文本转换的以下语言。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 359079ad73fcf162fb742afe74c1c1de5896c35c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996068"
---
# <a name="language-and-region-support-for-translator"></a>翻译人员的语言和区域支持

转换器支持文本转换的以下语言。 对于高质量的 AI 计算机翻译，神经计算机翻译（NMT）是新标准，可在神经系统可用时使用转换器的 V3 作为默认值。

[详细了解机器翻译的工作原理](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>翻译

**V2 转换器**

> [!NOTE]
> V2 已于2018年4月30日弃用。 请将你的应用程序迁移到 V3，以便充分利用 V3 中专门提供的新功能。

* 仅限统计：没有可用于此语言的神经系统。
* 可用神经：可用神经系统。 使用参数 `category=generalnn` 可访问神经系统。
* 神经默认值：神经是默认的翻译系统。 使用参数 `category=smt` 可访问适用于 Microsoft Translator Hub 的统计系统。
* 仅神经：仅神经翻译可用。

**V3 转换器**默认情况下，V3 转换器为神经，统计系统仅在不存在神经系统时可用。

> [!NOTE]
> 目前，可在自定义转换器中使用神经语言的子集，并逐渐添加其他翻译。 [查看自定义转换器中当前可用的语言](#customization)。

|语言|    语言代码|    V3 API|
|:-----|:-----:|:-----|
|南非荷兰语|    `af`|    神经|
|阿拉伯语|    `ar`    |    神经|
|Bangla|    `bn`    |    神经|
|波斯尼亚语(拉丁语系)|    `bs`    |    神经|
|保加利亚语|    `bg`    |    神经|
|粤语(繁体)|    `yue`|    统计|
|加泰罗尼亚语|    `ca`    |    统计|
|简体中文|    `zh-Hans`|神经|
|中文(繁体)|    `zh-Hant`        |神经|
|克罗地亚语|    `hr`    |神经|
|捷克语|    `cs`    |    神经|
|丹麦语|    `da`        |神经|
|荷兰语|    `nl`|    神经|
|英语|    `en`    |    神经|
|爱沙尼亚语|    `et`    |    神经|
|斐济语|    `fj`    |    统计|
|菲律宾语|    `fil`    |    统计|
|芬兰语|    `fi`    |    神经|
|法语|    `fr`    |    神经|
|德语|    `de`    |    神经|
|希腊语|    `el`    |    神经|
|古吉拉特语|    `gu`    |    神经|
|海地克里奥尔语|    `ht`        |统计|
|希伯来语    |`he`    |神经
|Hindi|    `hi`    |    神经|
|白苗文|    `mww`    |    统计|
|匈牙利语|    `hu`    |    神经|
|冰岛语|    `is`    |    神经|
|印度尼西亚语|    `id`    |    统计|
|爱尔兰语 | `ga`| 神经
|意大利语|    `it`    |    神经|
|日语|    `ja`    |    神经|
|卡纳达语|`kn`| 神经
|斯瓦希里语|    `sw`    |    统计|
|克林贡语|    `tlh`    |    统计|
|克林贡语(plqaD)|    `tlh-Qaak`    |    统计|
|韩语    |`ko`    |    神经|
|拉脱维亚语|    `lv`    |    神经|
|立陶宛语|    `lt`    |    神经|
|马达加斯加语|    `mg`    |    统计|
|马来语|    `ms`        |统计|
|马拉雅拉姆语| `ml` | 神经
|马耳他语|    `mt`    |    统计|
|毛利语| `mi`  | 神经|
|马拉地语| `mr`  | 神经|
|挪威语|    `nb`    |    神经|
|波斯语|    `fa`    |    神经|
|波兰语|    `pl`    |    神经|
|葡萄牙语(巴西)|    `pt-br`    |    神经|
|葡萄牙语(葡萄牙)| `pt-pt` | 神经
|旁遮普语|`pa`|神经
|克雷塔罗奥托米语|    `otq`    |    统计|
|罗马尼亚语|    `ro`    |    神经|
|俄语|    `ru`    |    神经|
|萨摩亚语|    `sm`    |    统计|
|塞尔维亚语（西里尔）|    `sr-Cyrl`|    统计|
|塞尔维亚语（拉丁）|    `sr-Latn`        |统计|
|斯洛伐克语|    `sk`    |    神经|
|斯洛文尼亚语|    `sl`    |    神经|
|西班牙语|    `es`    |    神经|
|瑞典语|    `sv`    |神经|
|塔希提语|    `ty`    |统计|
|泰米尔语|    `ta`    |    神经|
|泰卢固语|    `te`    |    神经|
|泰语|    `th`    |    神经|
|汤加语|    `to`    |    统计|
|土耳其语|    `tr`        |神经|
|乌克兰语|    `uk`    |    神经|
|乌尔都语|    `ur`    |    统计|
|越南语|    `vi`    |    神经|
|威尔士语|    `cy`    |    神经|
|尤卡坦玛雅语|    `yua`    |    统计|

> [!NOTE]
> 语言代码 `pt` 将默认为 `pt-br` 、葡萄牙语（巴西）。

## <a name="transliteration"></a>音译

Transliterate 方法支持以下语言。 在“音译方向”中，“<-->”表示该语言可以从列出的两侧脚本互相音译。 “-->” 表示该语言只能从一个脚本音译到另一个脚本。

| 语言    | 语言代码 | 脚本 | 到/从 | 脚本|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| 阿拉伯语 | `ar` | 阿拉伯语 `Arab` | <--> | 拉丁语 `Latn` |
|Bangla  | `bn` | 孟加拉语 `Beng` | <--> | 拉丁语 `Latn` |
| 中文(简体) | `zh-Hans` | 简体中文 `Hans`| <--> | 拉丁语 `Latn` |
| 中文(简体) | `zh-Hans` | 简体中文 `Hans`| <--> | 繁体中文 `Hant`|
| 中文(繁体) | `zh-Hant` | 繁体中文 `Hant`| <--> | 拉丁语 `Latn` |
| 中文(繁体) | `zh-Hant` | 繁体中文 `Hant`| <--> | 简体中文 `Hans` |
| 古吉拉特语 | `gu`  | 古吉拉特语 `Gujr` | --> | 拉丁语 `Latn` |
| 希伯来语 | `he` | 希伯来语 `Hebr` | <--> | 拉丁语 `Latn` |
| Hindi | `hi` | 梵语 `Deva` | <--> | 拉丁语 `Latn` |
| 日语 | `ja` | 日语 `Jpan` | <--> | 拉丁语 `Latn` |
| 卡纳达语 | `kn` | 卡纳达语 `Knda` | --> | 拉丁语 `Latn` |
| 马拉雅拉姆语 | `ml` | 马拉雅拉姆语 `Mlym` | --> | 拉丁语 `Latn` |
| 马拉地语 | `mr` | 梵语 `Deva` | --> | 拉丁语 `Latn` |
| 奥里雅语 | `or` | 奥里雅语 `Orya` | <--> | 拉丁语 `Latn` |
| 旁遮普语 | `pa` | 果鲁穆奇语 `Guru`  | <--> | 拉丁语 `Latn`  |
| 塞尔维亚语（西里尔） | `sr-Cyrl` | 西里尔语 `Cyrl`  | --> | 拉丁语 `Latn` |
| 塞尔维亚语（拉丁） | `sr-Latn` | 拉丁语 `Latn` | --> | 西里尔语 `Cyrl`|
| 泰米尔语 | `ta` | 泰米尔语 `Taml` | --> | 拉丁语 `Latn` |
| 泰卢固语 | `te` | 泰卢固语 `Telu` | --> | 拉丁语 `Latn` |
| 泰语 | `th` | 泰语 `Thai` | --> | 拉丁语 `Latn` |

## <a name="dictionary"></a>字典

字典使用 Lookup 和 Examples 方法支持以下将语言翻译成英语或将英语翻译为以下语言。

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

## <a name="detect"></a>Detect

转换器检测到可用于翻译和音译的所有语言。


## <a name="access-the-translator-language-list-programmatically"></a>以编程方式访问转换器语言列表

你可以使用 "语言" 方法检索翻译人员3.0 支持的语言的列表。 可按功能、语言代码以及该语言在英文或任何其他受支持语言中的名称来查看列表。 当有新语言可用时，Microsoft Translator 服务会自动更新此列表。

[查看语言操作参考文档](reference/v3-0-languages.md)

## <a name="customization"></a>自定义

以下语言可供使用[自定义转换器](https://aka.ms/CustomTranslator)的自定义或从英语进行自定义。

| 语言    | 语言代码 |
|:----------- |:-------------:|
| 阿拉伯语       | `ar`          |
| Bangla      | `bn`          |
| 波斯尼亚语(拉丁语系)      | `bs`          |
| 保加利亚语      | `bg`          |
| 简体中文      | `zh-Hans`          |
|中文(繁体)|    `zh-Hant`    |
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
| 希伯来语      | `he`          |
| Hindi      | `hi`          |
| 匈牙利语      | `hu`          |
| 冰岛语 | `is` |
| 印度尼西亚语|    `id`    |
| 爱尔兰语 | `ga`    |
| 意大利语      | `it`          |
| 日语      | `ja`          |
| 斯瓦希里语|    `sw`    |
| 韩语      | `ko`          |
| 拉脱维亚语      | `lv`          |
| 立陶宛语      | `lt`          |
| 马达加斯加语|    `mg`    |
| 毛利语| `mi`  |
| 挪威语      | `nb`          |
| 波斯语      | `fa`          |
| 波兰语      | `pl`          |
| 葡萄牙语(巴西) | `pt-br` |
| 罗马尼亚语      | `ro`          |
| 俄语      | `ru`          |
| 萨摩亚语|    `sm`    |
| 塞尔维亚语（拉丁）      | `sr-Latn`          |
| 斯洛伐克语     | `sk`          |
| 斯洛文尼亚语      | `sl`          |
| 西班牙语      | `es`          |
| 瑞典语      | `sv`          |
| 泰语      | `th`          |
| 土耳其语      | `tr`          |
| 乌克兰语      | `uk`          |
| 越南语      | `vi`          |
| 威尔士语 | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>访问 Microsoft Translator 网站上的列表

若要快速查看语言，Microsoft Translator 网站将显示翻译人员和语音 Api 支持的所有语言。 此列表不包括特定于开发者的信息，例如语言代码。

[查看语言列表](https://www.microsoft.com/translator/languages.aspx)
