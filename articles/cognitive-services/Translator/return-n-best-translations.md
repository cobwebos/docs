---
title: 使用 Microsoft 文本翻译 API 返回 N 个最佳翻译 | Microsoft Docs
description: 使用 Microsoft 文本翻译 API 返回 N 个最佳翻译。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 3eafe50f69ae1a6748342e64a414ecee4467d0d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365800"
---
# <a name="how-to-return-n-best-translations"></a>如何返回 N 个最佳翻译

> [!NOTE]
> 此方法已被废弃。 它在文本翻译 API V3.0 中不可用。

Microsoft Translator API 的 GetTranslations() 和 GetTranslationsArray() 方法包括一个可选的布尔标志“IncludeMultipleMTAlternatives”。
该方法将返回最多 maxTranslations 个替代项，其中，增量是通过翻译引擎的 N 个最佳项列表提供的。

签名为：

**语法**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**参数**

| 参数 | 说明 |
|:---|:---|
| appId | **必需** 如果使用了授权标头，请将 appid 字段保留为空，否则请指定一个包含 "Bearer" + " " + 访问令牌的字符串。|
| text | **必需** 一个字符串，表示要翻译的文本。 文本大小不得超过 10000 个字符。|
| from | **必需** 一个字符串，表示要翻译的文本的语言代码。 |
| to | **必需** 一个字符串，表示要将文本翻译成的语言代码。 |
| maxTranslations | **必需** 一个整数，表示要返回的最大翻译数。 |
| options | **可选** 一个 TranslateOptions 对象，包含下面列出的值。 它们是可选的，并且默认为最常用的设置。

* Category：唯一支持也是默认的选项为“general”。
* ContentType：唯一支持也是默认的选项为“text/plain”。
* State：用户状态，可帮助关联请求和响应。 响应中将返回相同的内容。
* IncludeMultipleMTAlternatives：一个标志，用于确定是否从 MT 引擎返回一个以上替代项。 默认值为 false，并且仅包括 1 个替代项。

## <a name="ratings"></a>评级
评级按下述方式应用：最佳自动翻译的评级为 5。
自动生成的（N 个最佳）翻译替代项的评级为 0，匹配度为 100。

## <a name="number-of-alternatives"></a>替代项数
返回的替代项的数目最多为 maxTranslations，但可以更少。

## <a name="language-pairs"></a>语言对
此功能不可用于简体中文与繁体中文之间的翻译（双向）。 它可用于 Microsoft Translator 支持的所有其他语言对。
