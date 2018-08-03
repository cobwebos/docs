---
title: Azure 内容审查器 - 文本审查 | Microsoft Docs
description: 对可能不需要的文本、PII 和自定义字词列表使用文本审查。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 6924807a64cec074d9688eaad158bb9bb638f6bb
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085753"
---
# <a name="text-moderation"></a>文本审查

使用内容审查器的计算机辅助文本审查和[人工评审](Review-Tool-User-Guide/human-in-the-loop.md)功能来审核文本内容。

可根据策略和阈值阻止、批准或审核内容。 用它来增强对合作伙伴、员工和消费者生成文本内容的环境的人工审核。 这些环境包括聊天室、讨论区、聊天机器人、电子商务目录和文档。 

服务响应包含以下信息：

- 不敬词：根据多种语言的内置不敬字词列表执行基于字词的匹配
- 分类：在机器的辅助下分类成三个类别
- 个人身份信息 (PII)
- 自动更正的文本
- 原始文本
- 语言

## <a name="profanity"></a>亵渎内容

如果 API 在任何[受支持语言](Text-Moderation-API-Languages.md)中检测到任何亵渎字词，这些字词会包含在响应中。 响应还会包含这些字词在原始文本中的位置 (`Index`)。 以下示例 JSON 中的 `ListId` 引用[自定义字词列表](try-terms-list-api.md)（如果有）中找到的字词。

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> 对于 **language** 参数，请分配 `eng` 或将其留空以查看机器辅助的**分类**响应（预览功能）。 **此功能仅支持英语**。
>
> 对于**亵渎字词**检测，请使用本文所列的支持语言的 [ISO 639-3 代码](http://www-01.sil.org/iso639-3/codes.asp)或将其留空。

## <a name="classification"></a>分类

内容审查器的机器辅助**文本分类功能**仅支持**英语**，可帮助检测可能不需要的内容。 根据上下文，可能会将标记的内容评估为不合适。 它传达了每个类别的可能性，并可能建议进行人工审核。 该功能使用训练的模型来识别可能的辱骂、贬损或歧视性语言。 要评审的内容包括俚语、缩写词、冒犯性言语，以及有意拼错的单词。 

以下 JSON 摘录内容显示了示例输出：

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
    }

### <a name="explanation"></a>说明

- `Category1` 表示可能存在某些情况下被视为色情或成人性质的语言。
- `Category2` 表示可能存在某些情况下被视为性暗示或过于成熟的语言。
- `Category3` 表示可能存在某些情况下被视为具攻击性的语言。
- `Score` 介于 0 和 1 之间。 评分越高，模型预测类别可能适用的可能性越高。 此预览版依赖于统计模型，而不是人工编码结果。 我们建议你对自己的内容进行测试，以确定每个类别是否符合要求。
- `ReviewRecommended` 为 true 或 false，具体情况取决于内部评分阈值。 客户应评估是使用该值，还是根据他们的内容策略确定自定义阈值。

## <a name="personally-identifiable-information-pii"></a>个人身份信息 (PII)

PII 功能检测可能存在以下信息：

- 电子邮件地址
- 美国邮寄地址
- IP 地址
- 美国电话号码
- 英国电话号码
- 社会安全号码 (SSN)

以下示例显示了示例响应：

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
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
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
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>自动更正

假设输入文本为（“lzay”和“f0x”是有意拼错的）：

    The qu!ck brown f0x jumps over the lzay dog.

如果请求执行自动更正，则响应会包含更正后的文本版本：

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>创建和管理自定义字词列表

尽管在默认情况下，全局字词列表能够很好地满足大部分需要，但你可能想要根据自己的具体业务需求筛选字词。 例如，你可能想要从用户的发布内容中，筛选出所有竞争品牌名称。

> [!NOTE]
> 最大限制为“5 个字词列表”，每个列表“不超过 10,000 个字词”。
>

以下示例显示匹配的列表 ID：

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

内容审查器提供[字词列表 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) 和相应的操作用于管理自定义字词列表。 请从[字词列表 API 控制台](try-terms-list-api.md)开始，使用 REST API 代码示例。 如果你熟悉 Visual Studio 和 C#，另请参阅[字词列表 .NET 快速入门](term-lists-quickstart-dotnet.md)。

## <a name="next-steps"></a>后续步骤

体验[文本审查 API 控制台](try-text-api.md)并使用 REST API 代码示例。 如果你熟悉 Visual Studio 和 C#，另请参阅[文本审查 .NET 快速入门](text-moderation-quickstart-dotnet.md)。
