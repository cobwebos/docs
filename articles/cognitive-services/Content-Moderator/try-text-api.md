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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272587"
---
# <a name="moderate-text-from-the-api-console"></a>从 API 控制台审查文本

在 Azure 内容审查器中使用[文本审查 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 扫描文本内容以查找亵渎语言，并将其与自定义和共享列表进行比较。

## <a name="get-your-api-key"></a>获取 API 密钥

必须有订阅密钥，才能在联机控制台中试用 API。 订阅密钥位于“设置”**** 选项卡上的“Ocp-Apim-Subscription-Key”**** 框中。 有关详细信息，请参阅[概述](overview.md)。

## <a name="navigate-to-the-api-reference"></a>导航到 API 参考

转到[文本审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)。 

  “文本 - 屏幕”**** 页随即打开。

## <a name="open-the-api-console"></a>打开 API 控制台

对于“开放 API 测试控制台”****，选择与所在位置最相关的区域。 

  ![“文本 - 屏幕”页区域选择](images/test-drive-region.png)

  文本 - 屏幕**** API 控制台随即打开。

## <a name="select-the-inputs"></a>选择输入

### <a name="parameters"></a>参数

在文本屏幕中选择要使用的查询参数。 在本例中，使用语言**** 的默认值。 也可以将其留空，因为作为其执行的一部分，该操作将自动检测可能的语言。

> [!NOTE]
> 对于 **language** 参数，请分配 `eng` 或将其留空以查看机器辅助的**分类**响应（预览功能）。 **此功能仅支持英语**。
>
> 对于不雅用语**** 检测，请使用本文所列的支持语言的 [ISO 639-3 代码](http://www-01.sil.org/iso639-3/codes.asp)或留空。

对于“自动更正”****、“PII”**** 和“分类（预览）”****，请选择“true”****。 将“ListId”**** 字段保留空白。

  ![文本 - 屏幕控制台查询参数](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>内容类型

有关 **Content-Type**，选择你想要显示的内容类型。 在本例中，使用默认的 text/plain**** 内容类型。 在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥****。

### <a name="sample-text-to-scan"></a>要扫描的示例文本

在“请求正文”**** 框中，输入一些文本。 以下示例显示文本中的有意拼写错误。

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>分析响应

下面的响应显示来自 API 的各种见解。 它包含潜在不雅内容、个人数据、分类（预览）和自动更正版本。

> [!NOTE]
> 计算机辅助的“分类”功能处于预览状态，仅支持英语。

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

有关 JSON 响应中各部分的详细说明，请参阅[文本审查](text-moderation-api.md)概念指南。

## <a name="next-steps"></a>后续步骤

在代码中使用 REST API，或按照[.NET SDK 快速入门](dotnet-sdk-quickstart.md)与应用程序集成。
