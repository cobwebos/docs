---
title: 通过使用 Azure 内容审查器中的文本审查 API 来审查文本 | Microsoft Docs
description: 通过使用联机控制台中的文本审查 API 体验文本审查。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: ed696c31a886626819414c45eb7995edaf161fff
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365841"
---
# <a name="moderate-text-from-the-api-console"></a>从 API 控制台审查文本

使用 Azure 内容审查器中的[文本审查 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 扫描文本内容。 此操作将对内容的不雅内容进行扫描，并将内容与自定义和共享黑名单进行比较。


## <a name="get-your-api-key"></a>获取 API 密钥
可以在联机控制台中体验 API 之前，需要提供订阅密钥。 密钥位于“设置”选项卡的“Ocp-Apim-Subscription-Key”框中。 有关详细信息，请参阅[概述](overview.md)。

## <a name="navigate-to-the-api-reference"></a>导航到 API 参考
转到[文本审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)。 

  “文本 - 屏幕”页随即打开。

## <a name="open-the-api-console"></a>打开 API 控制台
若要打开 API 测试控制台，选择最贴切地描述你所在位置的区域。 

  ![“文本 - 屏幕”页区域选择](images/test-drive-region.png)

  文本 - 屏幕 API 控制台随即打开。

## <a name="select-the-inputs"></a>选择输入

### <a name="parameters"></a>parameters
在文本屏幕中选择要使用的查询参数。 在本例中，使用语言的默认值。 也可以将其留空，因为作为其执行的一部分，该操作将自动检测可能的语言。

> [!NOTE]
> 有关 language 参数，分配 `eng` 或留空以查看计算机辅助的分类响应（预览功能）。 **此功能仅支持英语**。
>
> 对于不雅用语检测，请使用本文所列的支持语言的 [ISO 639-3 代码](http://www-01.sil.org/iso639-3/codes.asp)或留空。

对于“自动更正”、“PII”和“分类（预览）”，请选择“true”。 将“ListId”字段保留空白。

  ![文本 - 屏幕控制台查询参数](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>内容类型
有关 **Content-Type**，选择你想要显示的内容类型。 在本例中，使用默认的 text/plain 内容类型。 在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

### <a name="sample-text-to-scan"></a>要扫描的示例文本
在“请求正文”框中，输入一些文本。 以下示例显示文本中的有意拼写错误。

> [!NOTE]
> 以下示例文本中的无效社会安全号码是有意而为。 目的是传达示例输入和输出格式。

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>文本分类功能

在下面的示例中，你将看到内容审查器的计算机辅助文本分类响应。 它可帮助检测可能不需要的内容。 标记的内容可能会被视为不适合，具体取决于上下文。 除了传达每个类别的可能性，它可能会建议对内容进行人工评审。 该功能使用定型模型来标识可能辱骂、贬损或歧视性语言。 要检查的内容包括俚语、缩写词、冒犯性言语，以及有意拼写错误的单词。 

#### <a name="explanation"></a>说明

- `Category1` 表示可能存在某些情况下可能被视为色情或成人性质的语言。
- `Category2` 表示可能存在某些情况下可能被视为性暗示或过于成熟的语言。
- `Category3` 表示可能存在某些情况下可能被视为具攻击性的语言。
- `Score` 介于 0 和 1 之间。 分数越高，模型预测类别可能适用的可能性越高。 此预览版依赖于统计模型，而不是人工编码结果。 我们建议对你自己的内容进行测试，以确定每个类别是否满足你的需求。
- `ReviewRecommended` 为 true 或 false，具体情况取决于内部分数阈值。 客户应评估是使用该值，还是根据他们的内容策略确定自定义阈值。

### <a name="analyze-the-response"></a>分析响应
下面的响应显示来自 API 的各种见解。 它包含潜在不雅内容、PII、分类（预览）和自动更正版本。

> [!NOTE]
> 计算机辅助的“分类”功能处于预览状态，仅支持英语。

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

有关 JSON 响应中各部分的详细说明，请参阅[文本审查 API 概述](text-moderation-api.md)。

## <a name="next-steps"></a>后续步骤

若要与你的应用程序集成，请在代码中使用 REST API，或者从[文本审查 .NET 快速入门](text-moderation-quickstart-dotnet.md)开始。
