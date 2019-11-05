---
title: 容器限制-LUIS
titleSuffix: Azure Cognitive Services
description: 支持的 LUIS 容器语言。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507854"
---
# <a name="language-understanding-luis-container-limitations"></a>语言理解（LUIS）容器限制

LUIS 容器有几个值得注意的限制。 从支持的依赖项到支持的语言子集，本文详细介绍了这些限制。

## <a name="supported-dependencies-for-latest-container"></a>支持的 `latest` 容器依赖项

[Build/2019](https://news.microsoft.com/build2019/)上发布的最新 LUIS 容器将支持：

* [新的预生成的域](luis-reference-prebuilt-domains.md)：这些聚焦于企业的域包括实体、示例话语以及模式。 扩展这些供自己使用的域。

## <a name="unsupported-dependencies-for-latest-container"></a>不支持的 `latest` 容器依赖项

若要[导出容器](luis-container-howto.md#export-packaged-app-from-luis)，必须从 LUIS 应用中删除不受支持的依赖项。 尝试导出容器时，LUIS 门户会报告需要删除的这些不受支持的功能。

如果 LUIS 应用程序不包括以下任何依赖项，则可以使用该应用程序：

不受支持的应用配置|详细信息|
|--|--|
|不受支持的容器区域性| 荷兰语（`nl-NL`）<br>日语（`ja-JP`）<br>仅 [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions) 支持德语。|
|所有区域性不支持的实体|所有区域性的 [KeyPhrase](luis-reference-prebuilt-keyphrase.md) 预生成实体|
|不受支持的英语（`en-US`）区域性实体|[GeographyV2](luis-reference-prebuilt-geographyV2.md) 预生成实体|
|语音启动|容器中不支持外部依赖项。|
|情绪分析|容器中不支持外部依赖项。|
|必应拼写检查|容器中不支持外部依赖项。|

## <a name="languages-supported"></a>支持的语言

LUIS 容器支持 LUIS[支持的语言](luis-language-support.md#languages-supported)子集。 LUIS 容器能够理解以下语言的最谈话：

| 语言 | Locale | 预生成域 | 预构建实体 | 短语列表建议 | **[文本分析](../text-analytics/language-support.md)<br>（情绪和<br>关键字）|
|--|--|:--:|:--:|:--:|:--:|
| 美国英语 | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[中文](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| 法语(法国) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| 法语(加拿大) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| 德语 |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| 印地语 | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| 意大利语 |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| 韩语 |`ko-KR` | ✔️ | ❌ | ❌ | 仅限*关键短语* |
| 葡萄牙语(巴西) |`pt-BR` | ✔️ | ✔️ | ✔️ | 并非所有亚区域性 |
| 西班牙语(西班牙) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| 西班牙语(墨西哥)|`es-MX` | ❌ | ❌ |✔️|✔️|
| 土耳其语 | `tr-TR` |✔️| ❌ | ❌ | 仅*情绪* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]