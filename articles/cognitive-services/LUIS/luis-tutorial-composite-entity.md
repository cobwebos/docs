---
title: 创建复合实体提取复杂数据 - Azure | Microsoft Docs
description: 了解如何在 LUIS 应用中创建复合实体来提取不同类型的实体数据。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264379"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>使用复合实体提取复杂数据
此简单应用具备两个[意向](luis-concept-intent.md)和几个实体。 其目的是预定航班，例如“1 张周五从西雅图到开罗的机票”，并以一段数据返回此次预定的所有详细信息。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
* 添加预生成实体 datetimeV2 和数量
* 创建一个复合实体
* 对 LUIS 进行查询并接收复合实体数据

## <a name="before-you-begin"></a>开始之前
* [分层快速入门](luis-tutorial-composite-entity.md)中的 LUIS 应用。 

> [!Tip]
> 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/free/)。

## <a name="composite-entity-is-a-logical-grouping"></a>复合实体一种逻辑分组 
实体的用途是查找话语中的文本部分并将其分类。 [复合](luis-concept-entity-types.md)实体由从上下文了解的其他实体类型组成。 对于用于预定航班的此旅行应用，有几个部分的信息，例如日期、地点和座位数。 

在创建复合实体之前，这些现有信息都是单独的实体。 如果可按逻辑对单独的实体分组，且这种逻辑分组可帮助聊天机器人或其他使用 LUIS 的应用程序，请创建一个复合实体。 

用户提供的简单示例话语包括：

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
复合实体匹配座位数、出发地点、目的地和日期。 

## <a name="what-luis-does"></a>LUIS 的作用
在[终结点](https://aka.ms/luis-endpoint-apis)中识别、[提取](luis-concept-data-extraction.md#list-entity-data)并以 JSON 格式返回话语的意向和实体后，LUIS 即告完成。 调用方应用程序或聊天机器人按照设计的任何方式提取该 JSON 响应并履行请求。 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>添加预生成实体数量和 datetimeV2
1. 从 [LUIS][LUIS] 网站上的应用列表中选择 `MyTravelApp` 应用。

2. 应用打开时，选择左侧的“实体”导航链接。

    ![选择实体按钮](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. 选择“管理预生成实体”。

    ![选择实体按钮](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. 在弹出框中选择“数量”和“datetimeV2”。

    ![选择实体按钮](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. 为了提取新的实体，请选择顶部导航栏中的“训练”。

    ![选择训练按钮](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>使用现有意向创建复合实体
1. 从左侧导航栏中选择“意向”。 

    ![选择“意向”页面](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. 在“意向”列表中选择 `BookFlight`。  

    ![从列表中选择 BookFlight 意向](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    话语上标记了数量和 datetimeV2 预生成实体。

3. 至于话语 `book 2 flights from seattle to cairo next monday`，请选择蓝色的 `number` 实体，然后从列表选择“包装进复合实体”。 字词下方的绿线随着游标向右移动，指示复合实体的内容。 然后移至右侧，选择最后一个预生成实体 `datetimeV2`，然后在弹出窗口的文本框中输入 `FlightReservation`，并选择“新建复合实体”。 

    ![在意向页面上创建复合实体](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. 随即会弹出一个对话框，用于验证复合实体的组成部分。 选择“完成”。

    ![在意向页面上创建复合实体](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>将实体包装进复合实体
创建复合实体后，请在复合实体中标记剩余话语。 若要将短语包装为复合实体，需选择最左边的字词并从显示的列表中选择“包装进复合实体”，然后选择最右边的字词并选择已命名的复合实体 `FlightReservation`。 此选择操作快速、流畅，细分为以下骤：

1. 在话语 `schedule 4 seats from paris to london for april 1` 中选择“4”作为数量预生成实体。

    ![选择最左边的字词](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. 从显示的列表中选择“包装进复合实体”。

    ![从列表中选择包装](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. 选择最右边的字词。 该短语下方显示了一条绿线，指示复合实体的内容。

    ![选择最右边的字词](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. 从显示的列表中选择复合名称 `FlightReservation`。

    ![选择已命名的复合实体](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    对于最后一个话语，请将 `London` 和 `tomorrow` 包装进复合实体，使用相同的说明。 

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![训练应用](./media/luis-tutorial-composite-entity/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

2. 选择“生产”槽和“发布”按钮。

    ![发布应用](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的话语查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    ![选择终结点 URL](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. 将光标定位到地址中 URL 的末尾，并输入 `reserve 3 seats from London to Cairo on Sunday`。 最后一个查询字符串参数为 `q`，表示话语查询。 此话语不同于标记的任何话语，因此，它非常适合用于测试，测试结果应返回包含所提取的分层实体的 `BookFlight` 意向。

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

此话语返回一个包含 flightreservation 对象的复合实体数组，且带有已提取的数据。  

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用只包含两个意向和一个复合实体，识别了自然语言查询意向，并返回了提取的数据。 

现在，聊天机器人已获得足够的信息，可以确定主要操作 `BookFlight` 以及在话语中找到的预留信息。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="next-steps"></a>后续步骤

[详细了解实体](luis-concept-entity-types.md)。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
