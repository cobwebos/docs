---
title: LUIS 中的预构建实体 | Microsoft Docs
description: 本文包含了语言理解智能服务 (LUIS) 中包括的预构建实体的列表。
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: 4858de8b8517016ca385e9105ca8948b66aa683b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321799"
---
# <a name="prebuilt-entities"></a>预构建实体

LUIS 包括一组预构建实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 预构建实体支持因 LUIS 应用的区域性而异。 有关 LUIS 支持的预构建实体的完整列表，包括区域性支持，请参阅[预构建实体参考](./luis-reference-prebuilt-entities.md)。

> [!NOTE]
> **builtin.datetime** 已弃用。 它由 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) 取代，后者提供日期和时间范围的识别，并改进了对模糊日期和时间的识别。

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

1. 单击“我的应用”页上的应用名称打开应用，然后单击左侧的“实体”。 
2. 在“实体”页上，单击“管理预构建实体”。

    ![“实体”页 - 管理预构建实体](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. 在“添加预构建实体”对话框中，单击要添加的预构建实体（例如，“datetimeV2”）。 然后单击“保存”。

    ![“添加预构建实体”对话框](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>使用预构建数字实体
当预构建实体包含在应用程序中时，其预测将包含在发布的应用程序中。 预构建实体的行为是预先训练的，**不能**修改的。 请按照以下步骤了解预构建实体的工作原理：

1. 在应用中添加**数字**实体，然后[训练](interactive-test.md)并[发布](PublishApp.md)该应用。
2. 单击“发布应用”页中的终结点 URL，在 Web 浏览器中打开 LUIS 终结点。 
3. 将陈述追加到包含数字表达式的 URL 后面。 例如，可以键入 `buy two plane ticktets`，并看到 LUIS 将 `two` 标识为 `builtin.number` 实体，并在 `resolution` 字段中将 `2` 标识为其值。 `resolution` 字段可帮助你将数字和日期解析为规范形式，以便客户端应用程序更轻松地使用。 

    ![包含数字实体的浏览器中的陈述](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS 可以智能识别非标准形式的数字。 在陈述中尝试不同的数字表达式，看看 LUIS 返回什么。

下面的示例显示了来自 LUIS 的 JSON 响应，其中包括了对陈述“two dozen”的值 24 的解析。

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>使用预构建的 datetimeV2 实体
**datetimeV2** 预构建实体可识别日期、时间、日期范围和持续时间。 请按照以下步骤了解 `datetimeV2` 预构建实体的工作原理：

1. 在应用中添加 **datetimeV2** 实体，然后[训练](interactive-test.md)并[发布](PublishApp.md)该应用。
2. 单击“发布应用”页中的终结点 URL，在 Web 浏览器中打开 LUIS 终结点。 
3. 将陈述追加到包含日期范围的 URL 后面。 例如，可以键入 `book a flight tomorrow`，并看到 LUIS 将 `tomorrow` 标识为 `builtin.datetimeV2.date` 实体，并在 `resolution` 字段中将明天的日期标识为其值。 

以下示例显示了如果今天的日期是 2017 年 10 月 31 日，来自 LUIS 的 JSON 响应可能会是什么样子。

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [预构建实体参考](./luis-reference-prebuilt-entities.md)
