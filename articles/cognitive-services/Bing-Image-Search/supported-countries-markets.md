---
title: Azure 上的必应图像搜索 API 支持的国家/地区和语言 | Microsoft Docs
description: 找出必应图像搜索 API 支持哪些国家/地区和语言。
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366434"
---
# <a name="bing-image-search-countries-and-languages"></a>必应图像搜索国家/地区和语言

必应图像搜索 API 支持超过 36 个国家/地区，多个国家/地区具有多种语言。 指定具有查询的国家/地区主要用于根据该国家/地区的兴趣来优化搜索结果。 此外，结果可能包含到必应的链接，这些链接可能会根据指定的国家/地区或语言本地化必应用户体验。

若要指定国家/地区和语言，请将 `mkt`（市场）查询参数设置为下面的“市场”表中的一个代码。 市场同时指定国家/地区和语言。 如果用户希望以其他语言查看显示文本，请将 `setLang` 查询参数设置为相应的语言代码。

另外，也可以使用 `cc` 查询参数指定国家/地区。 如果指定了国家/地区，还必须使用 `Accept-Language` HTTP 标头指定一个或多个语言代码。 支持的语言因国家/地区而异；“市场”表中提供了适用于每个国家/地区的语言。

> [!NOTE]
> 热门图像 API 目前仅支持以下市场：
> - en-US（英语，美国） 
> - en-CA（英语，加拿大） 
> - en-AU（英语，澳大利亚） 
> - zh-CN（中文，中国）

## <a name="countries"></a>国家/地区

|国家/地区|代码|
|-------|----|
|阿根廷|AR|
|澳大利亚|AU|
|奥地利|AT|
|比利时|BE|
|巴西|BR|
|加拿大|CA|
|智利|CL|
|丹麦|DK|
|芬兰|FI|
|法国|FR|
|德国|DE|
|香港特别行政区|HK|
|印度|IN|
|印度尼西亚|ID|
|意大利|IT|
|日本|JP|
|韩国|KR|
|马来西亚|MY|
|墨西哥|MX|
|荷兰|NL|
|新西兰|NZ|
|挪威|否|
|中国|CN|
|波兰|PL|
|葡萄牙|PT|
|菲律宾|PH|
|俄罗斯|RU|
|沙特阿拉伯|SA|
|南非|ZA|
|西班牙|ES|
|瑞典|SE|
|瑞士|CH|
|中国台湾|TW|
|土耳其|TR|
|英国|GB|
|美国|美国|


## <a name="markets"></a>市场

|国家/地区|语言|市场代码|
|-------|--------|-----------|
|阿根廷|西班牙语|es-AR|
|澳大利亚|英语|en-AU|
|奥地利|德语|de-AT|
|比利时|荷兰语|nl-BE|
|比利时|法语|fr-BE|
|巴西|葡萄牙语|pt-BR|
|加拿大|英语|en-CA|
|加拿大|法语|fr-CA|
|智利|西班牙语|es-CL|
|丹麦|丹麦语|da-DK|
|芬兰|芬兰语|fi-FI|
|法国|法语|fr-FR|
|德国|德语|de-DE|
|香港特别行政区|繁体中文|zh-HK|
|印度|英语|en-IN|
|印度尼西亚|英语|en-ID|
|意大利|意大利语|it-IT|
|日本|日语|ja-JP|
|韩国|韩语|ko-KR|
|马来西亚|英语|en-MY|
|墨西哥|西班牙语|es-MX|
|荷兰|荷兰语|nl-NL|
|新西兰|英语|en-NZ|
|中国|中文|zh-CN|
|波兰|波兰语|pl-PL|
|葡萄牙|葡萄牙语|pt-PT|
|菲律宾|英语|en-PH|
|俄罗斯|俄语|ru-RU|
|沙特阿拉伯|阿拉伯语|ar-SA|
|南非|英语|en-ZA|
|西班牙|西班牙语|es-ES|
|瑞典|瑞典语|sv-SE|
|瑞士|法语|fr-CH|
|瑞士|德语|de-CH|
|中国台湾|繁体中文|zh-TW|
|土耳其|土耳其语|tr-TR|
|英国|英语|en-GB|
|美国|英语|en-US|
|美国|西班牙语|es-US|

## <a name="next-steps"></a>后续步骤
有关必应新闻搜索终结点的详细信息，请参阅[新闻图像搜索 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)。
