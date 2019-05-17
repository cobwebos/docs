---
title: 语言支持 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 必应新闻搜索 API 支持的自然语言、国家/地区和区域列表。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: e657c4678c76e8ff667c1a3f30409fc157f52d16
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798246"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>必应 Web 搜索 API 的语言和区域支持

必应 Web 搜索 API 支持超过 36 个国家或地区，其中很多具有多种语言。 使用查询指定国家或地区有助于根据对该国家或地区的兴趣来优化搜索结果。 结果可能包含指向必应的链接，这些链接可能会根据指定的国家/地区或语言本地化必应用户体验。

可以使用 `cc` 查询参数指定国家或地区。 指定国家或地区后，必须使用[`Accept-Language` 标头](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)指定一个或多个语言代码。 使用[市场表](#markets)获取每个市场支持的语言列表。

或者，可以使用 `mkt` 查询参数和市场表中的代码指定市场。 指定市场的同时指定国家或地区和首选语言。 可以显式使用 `setLang` 查询参数设置语言。

## <a name="countriesregions"></a>国家/地区

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
|中国香港特别行政区|HK|
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
|中国香港特别行政区|繁体中文|zh-HK|
|印度|英语|en-IN|
|印度尼西亚|英语|en-ID|
|意大利|意大利语|it-IT|
|日本|日语|ja-JP|
|韩国|韩语|ko-KR|
|马来西亚|英语|en-MY|
|墨西哥|西班牙语|es-MX|
|荷兰|荷兰语|nl-NL|
|新西兰|英语|en-NZ|
|挪威|挪威语|no-NO|
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

* [必应图像搜索 API 参考](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
