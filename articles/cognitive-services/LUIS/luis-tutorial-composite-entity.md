---
title: 教程：创建复合实体提取复杂数据 - Azure | Microsoft Docs
description: 了解如何在 LUIS 应用中创建复合实体来提取不同类型的实体数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 5f11409ff49830be97d9a13a0ab7f033d9cc1041
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494459"
---
# <a name="tutorial-6-add-composite-entity"></a>教程：6. 添加复合实体 
在本教程中，添加复合实体以将提取的数据捆绑到包含的实体中。

本教程介绍如何执行下列操作：

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解复合实体 
> * 添加复合实体提取数据
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>开始之前
如果尚未获得[分层实体](luis-quickstart-intent-and-hier-entity.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) Github 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `composite`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。  

## <a name="composite-entity-is-a-logical-grouping"></a>复合实体一种逻辑分组 
复合实体的目的是将相关实体分组为父类别实体。 在创建复合实体之前，这些现有信息都是单独的实体。 它类似于分层实体，但可以包含更多类型的实体。 

 如果可按逻辑对单独的实体分组，且这种逻辑分组可帮助客户端应用程序，请创建一个复合实体。 

在此应用中，员工姓名在“员工”列表实体中定义，包括姓名、电子邮件地址、公司电话分机号、移动电话号码和美国联邦纳税人标识号的同义词。 

“MoveEmployee”意向具有示例话语，用于请求员工从一个建筑物和办公室移动到另一个建筑物和办公室。 建筑物名称用字母表示：“A”、“B”等，而办公室用数字表示：“1234”、“13245”。 

“MoveEmployee”意向中的示例话语包括：

|示例陈述|
|--|
|将 John W . Smith 调动到 a-2345|
|明天将 x12345 换到 h-1234|
 
调动请求应至少包括员工（使用任何同义词），以及最终的建筑物和办公室位置。 请求还可以包括原始的办公室以及应该执行调动的日期。 

从终结点提取的数据应包含此信息，并在 `RequestEmployeeMove` 复合实体中返回。 

## <a name="create-composite-entity"></a>创建复合实体
1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

2. 在“意向”页上，选择“MoveEmployee”意向。 

3. 选择工具栏上的放大镜图标以筛选话语列表。 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已突出显示放大镜按钮")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. 在筛选器文本框中输入 `tomorrow` 以查找话语 `shift x12345 to h-1234 tomorrow`。

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已突出显示键入了“tomorrow”的筛选器")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    另一种方法是通过 datetimeV2 筛选实体，通过选择“实体筛选器”，然后从列表中选择“datetimeV2”。 

5. 选择第一个实体 `Employee`，然后在弹出菜单列表中选择“在复合实体中包装”。 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已突出显示选择复合中的第一个实体")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. 然后立即选择最后一个实体，话语中的 `datetimeV2`。 在所选字词下面绘制的绿色条指示复合实体。 在弹出菜单中，输入复合名称 `RequestEmployeeMove` 然后在弹出菜单上选择“新建复合”。 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已突出显示选择复合中的最后一个实体和创建实体")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. 在“想要创建哪种类型的实体?”中，列表中包含几乎所有所需字段。 仅缺失原始位置。 选择“添加子实体”，从现有实体列表中选择“位置::原始”，然后选择“完成”。 

    ![“MoveEmployee”意向上的 LUIS 屏幕截图，在弹出窗口中添加其他实体](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. 选择工具栏上的放大镜以删除筛选器。 

## <a name="label-example-utterances-with-composite-entity"></a>使用复合实体标记示例话语
1. 在每个示例话语中，选择应在复合中的最左侧实体。 然后选择“在复合实体中包装”.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已突出显示选择复合中的第一个实体")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. 选择复合实体中的最后一个单词，然后从弹出菜单中选择“RequestEmployeeMove”。 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已突出显示选择复合中的最后一个实体")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. 验证意向中的所有话语都已使用复合实体进行标记。 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "“MoveEmployee”意向上的 LUIS 屏幕截图，其中已标记所有话语")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>查询终结点 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`。 最后一个查询字符串参数为 `q`，表示话语查询。 

    由于此测试是为了验证是否正确提取复合，因此测试可以包括现有的示例话语或新话语。 一个有效的测试是在复合实体中包含所有子实体。

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  此话语返回复合实体数组。 每个实体都有类型和值。 若要查找每个子实体的更高精度，请使用复合数组项中的类型和值的组合来查找实体数组中的相应项。  

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用识别了自然语言查询意向，并将提取的数据作为命名组返回。 

现在，聊天机器人已获得足够的信息，可以确定主要操作和话语中的相关详细信息。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [了解如何使用短语列表添加简单实体](luis-quickstart-primary-and-secondary-data.md)  