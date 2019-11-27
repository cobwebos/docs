---
title: 使用文本审查 API 来审查文本 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 通过使用联机控制台中的文本审查 API 体验文本审查。
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538825"
---
# <a name="moderate-text-from-the-api-console"></a>从 API 控制台审查文本

在 Azure 内容审查器中使用[文本审查 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 扫描文本内容以查找亵渎语言，并将其与自定义和共享列表进行比较。

## <a name="get-your-api-key"></a>获取 API 密钥

需要提供订阅密钥才能在联机控制台中试运行 API。 订阅密钥位于“设置”选项卡上的“Ocp-Apim-Subscription-Key”框中。 有关详细信息，请参阅[概述](overview.md)。

## <a name="navigate-to-the-api-reference"></a>导航到 API 参考

转到[文本审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)。 

  “文本 - 屏幕”页随即打开。

## <a name="open-the-api-console"></a>打开 API 控制台

对于“开放 API 测试控制台”，选择与所在位置最相关的区域。 

  ![“文本 - 屏幕”页区域选择](images/test-drive-region.png)

  文本 - 屏幕 API 控制台随即打开。

## <a name="select-the-inputs"></a>选择输入

### <a name="parameters"></a>Parameters

在文本屏幕中选择要使用的查询参数。 在本例中，使用语言的默认值。 也可以将其留空，因为作为其执行的一部分，该操作将自动检测可能的语言。

> [!NOTE]
> 对于 **language** 参数，请分配 `eng` 或将其留空以查看机器辅助的**分类**响应（预览功能）。 **此功能仅支持英语**。
>
> 对于**不敬词**检测，请使用本文所列的支持语言的 [ISO 639-3 代码](http://www-01.sil.org/iso639-3/codes.asp)或将其留空。

对于“自动更正”、“PII”和“分类（预览）”，请选择“true”。 将“ListId”字段保留空白。

  ![文本 - 屏幕控制台查询参数](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>内容类型

有关 **Content-Type**，选择你想要显示的内容类型。 在本例中，使用默认的 text/plain 内容类型。 在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

### <a name="sample-text-to-scan"></a>要扫描的示例文本

在“请求正文”框中，输入一些文本。 以下示例显示文本中的有意拼写错误。

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>分析响应

下面的响应显示来自 API 的各种见解。 它包含潜在不雅内容、个人数据、分类（预览）和自动更正版本。

> [!NOTE]
> 计算机辅助的“分类”功能处于预览状态，仅支持英语。

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

有关 JSON 响应中各部分的详细说明，请参阅[文本审查](text-moderation-api.md)概念指南。

## <a name="next-steps"></a>后续步骤

请在代码中使用 REST API，或按照[.NET SDK 快速入门](dotnet-sdk-quickstart.md)与应用程序集成。
