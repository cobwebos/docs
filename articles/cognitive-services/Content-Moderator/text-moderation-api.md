---
title: 文本审查 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 对于可能不需要的文本、个人数据和自定义的字词列表，请使用文本审核。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: 5f41330836edab647f379eb43130c078c46cce53
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685062"
---
# <a name="learn-text-moderation-concepts"></a>了解文本审查概念

使用内容审查器的文本审核模型来分析文本内容。

您可以根据您的策略和阈值来阻止、批准或查看内容（请参阅[评审、工作流和作业](./review-api.md)了解如何设置人为审阅）。 使用文本审核模型，可以在合作伙伴、员工和消费者生成文本内容的环境中增加人员审核。 这些环境包括聊天室、讨论区、聊天机器人、电子商务目录和文档。 

服务响应包含以下信息：

- 不敬词：根据多种语言的内置不敬字词列表执行基于字词的匹配
- 分类：在机器的辅助下分类成三个类别
- 个人数据
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
> 对于不雅用语  检测，请使用本文所列的支持语言的 [ISO 639-3 代码](http://www-01.sil.org/iso639-3/codes.asp)或留空。

## <a name="classification"></a>分类

内容审查器的计算机辅助**文本分类功能**仅支持**英语**，有助于检测可能不需要的内容。 根据上下文，可能会将标记的内容评估为不合适。 它传达了每个类别的可能性，并可能建议进行人工审核。 该功能使用训练的模型来识别可能的辱骂、贬损或歧视性语言。 要评审的内容包括俚语、缩写词、冒犯性言语，以及有意拼错的单词。 

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
- `Score` 介于 0 和 1 之间。 评分越高，模型预测类别可能适用的可能性越高。 此功能依赖于统计模型，而不是人工编码结果。 我们建议你对自己的内容进行测试，以确定每个类别是否符合要求。
- `ReviewRecommended` 为 true 或 false，具体情况取决于内部评分阈值。 客户应评估是使用该值，还是根据他们的内容策略确定自定义阈值。

## <a name="personal-data"></a>个人数据

个人数据功能检测到此信息的潜在状态：

- 电子邮件地址
- 美国邮寄地址
- IP 地址
- 美国电话号码

以下示例显示了示例响应：

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>自动更正

假设输入文本为（故意为 "lzay" 和 "f0x"）：

    The qu!ck brown f0x jumps over the lzay dog.

如果请求执行自动更正，则响应会包含更正后的文本版本：

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>创建和管理自定义字词列表

尽管在默认情况下，全局字词列表能够很好地满足大部分需要，但你可能想要根据自己的具体业务需求筛选字词。 例如，你可能想要从用户的发布内容中，筛选出所有竞争品牌名称。

> [!NOTE]
> 最多只能使用 5 个术语列表****，每个列表中的术语数不得超过 10,000 个****。
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

利用[文本审核 api 控制台](try-text-api.md)测试 api。 另请参阅[评审、工作流和作业](./review-api.md)，了解如何设置人为审阅。
