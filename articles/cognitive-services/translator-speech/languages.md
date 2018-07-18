---
title: Microsoft 语音翻译 API 中支持的语言 | Microsoft Docs
description: 查看 Microsoft 语音翻译 API 支持的语言。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 0d33033442a012290baa78d80f1b8bde0499b3f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366820"
---
# <a name="languages-supported-by-the-microsoft-translator-speech-api"></a>Microsoft 语音翻译 API 支持的语言
语音翻译支持以下语言。 如果两种语言都支持语音翻译，则语音到语音翻译和语音到文本翻译都可用。 如果目标语言不支持语音转换，则只有语音到文本翻译可用。 

| 语音语言    |
|:----------- |
| 阿拉伯语(现代标准)      |
| 中文(普通话)      |
| 英语      |
| 法语      |
| 德语      |
| 意大利语      |
| 日语      |
| 葡萄牙语(巴西)     |
| 俄语      |
| 西班牙语      | 

Microsoft 语音翻译 API 支持以下列语言作为目标语言进行语音到文本翻译。 

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
|冰岛语|`is`          |
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

## <a name="access-the-list-programmatically"></a>以编程方式访问列表

可以使用语言资源以编程方式访问支持的语言列表。 此列表提供了语言代码以及以英语或任何其他受支持语言显示的语言名称。 当有新语言可用时，Microsoft Translator 服务会自动更新此列表。

语言资源返回支持进行语音、文本以及文本到语音翻译的语言列表。 语言资源不要求进行身份验证。

[访问 API 参考来尝试语言方法](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>访问 Microsoft Translator 网站上的列表

若要快速查看语言，Microsoft Translator 网站上显示了文本翻译和语音 API 支持的所有语言。 此列表不包括特定于开发者的信息，例如语言代码。

[查看语言列表](https://www.microsoft.com/translator/languages.aspx) 
