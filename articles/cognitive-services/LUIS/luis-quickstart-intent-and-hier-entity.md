---
title: 分层实体
titleSuffix: Azure Cognitive Services
description: 基于上下文查找相关的数据片段。 例如，对于从一个建筑物和办公室到另一个建筑物和办公室的物理移动，源位置和目标位置是相关的。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: dea623684454d4b14a370251df10599eb03bbb34
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856914"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>教程：从陈述中提取上下文相关的数据

在本教程中，基于上下文查找相关的数据片段。 例如，从一个城市转移到另一个城市的原位置和目的地。 可能同时需要这两个数据片段，并且它们彼此相关。  

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建新应用
> * 添加意向 
> * 添加包含出发地和目的地子级的位置分层实体
> * 定型
> * 发布
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>分层数据

此应用确定某位员工从原始城市移动到目的地城市的位置。 它使用分层实体来确定陈述中的位置。 

分层实体非常适合此类数据，因为这两个数据片段、子位置：

* 是简单实体。
* 在使用陈述的情况下彼此相关。
* 使用特别选择的词汇来指示每个实体。 这些词汇的示例包括：from/to（从/到）、leaving/headed to（离开/前往）、away from/toward（离开/前往）。
* 两个子级往往在同一个陈述中。 
* 需要由客户端应用作为一个信息单元进行分组和处理。

## <a name="create-a-new-app"></a>创建新应用

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>创建在城市之间移动员工的意向

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 选择“创建新意向”。 

1. 在弹出对话框中输入 `MoveEmployeeToCity`，然后选择“完成”。 

    ![“创建新意向”对话框的屏幕截图](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |将 John W.Smith 从西雅图派往达拉斯|
    |将 Jill Jones 从西雅图调至开罗|
    |将 John Jackson 从坦帕调至亚特兰大 |
    |将 Debra Doughtery 从达拉斯派往俄克拉荷马|
    |将 Jill Jones 从开罗派往坦帕|
    |将 Alice Anderson 从雷德蒙德调至奥克兰|
    |将 Carl Chamerlin 从旧金山派往雷德蒙德|
    |将 Steve Standish 从圣地亚哥调至贝尔维尤 |
    |将 Tanner Thompson 从堪萨斯城派往芝加哥|

    [ ![LUIS 的屏幕截图，在 MoveEmployee 意向中有新陈述](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>创建位置实体
LUIS 需要通过在陈述中标记原始位置和目标位置来了解什么是位置。 如需在令牌（原始）视图中查看陈述，请在标记为“实体视图”的陈述上方的栏中选择切换。 切换开关以后，此控件会被标记为“令牌视图”。

考虑以下陈述：

```json
move John W. Smith leaving Seattle headed to Dallas
```

该陈述中指定了两个位置：`Seattle` 和 `Dallas`。 它们都作为分层实体 `Location` 的子级，因为需要从陈述中提取这两种数据片段来完成客户端应用程序中的请求，而且这两种数据片段彼此相关。 
 
如果只存在分层实体的一个子级（出发地或目的地），仍会提取该子级。 不需要找到所有子级即可提取一个或一些子级。 

1. 在陈述 `move John W. Smith leaving Seattle headed to Dallas` 中，选择单词 `Seattle`。 此时会出现下拉菜单，其顶部有一个文本框。 在文本框中输入实体名称 `Location`，然后在下拉菜单中选择“创建新实体”。 

    [![在意向页上创建新实体的屏幕截图](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Screenshot of creating new entity on intent page")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. 在弹出窗口中，选择包含 `Origin` 和 `Destination` 子实体的“分层”实体类型。 选择“完成”。

    ![新建位置实体的实体创建弹出对话框的屏幕截图](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Screenshot of entity creation pop-up dialog for new Location entity")

1. `Seattle` 的标签已标记为 `Location`，因为 LUIS 不知道名词是出发地还是目的地，或者两者都不是。 依次选择 `Seattle`、“位置”，遵循菜单操作，然后选择 `Origin`。

    [![实体标签弹出对话框的屏幕截图，该对话框用于更改位置实体子级](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Screenshot of entity labeling pop-up dialog to change locations entity child")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. 在其他所有陈述中标记其他位置。 所有位置都标记以后，陈述开始显示出某种模式。 

    [![在陈述中标记的位置实体的屏幕截图](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Screenshot of Locations entity labeled in utterances")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    红色下划线表示 LUIS 对该实体不太确信。 训练可以解决这一问题。 

## <a name="add-example-utterances-to-the-none-intent"></a>将话语示例添加到 None 意向 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>训练应用，以便可以测试对意向所做的更改 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>发布应用，以便可以从终结点查询已训练的模型

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>从终结点获取意向和实体预测结果

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. 将光标定位到地址栏中 URL 的末尾，并输入 `Please move Carl Chamerlin from Tampa to Portland`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 此话语不同于标记的任何话语，因此，它非常适合用于测试，测试结果应返回包含所提取的分层实体的 `MoveEmployee` 意向。

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    预测了正确的意向，并且实体数组在对应的“entities”属性中同时具有原位置和目的地值。
    
## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相关信息

* [分层实体](luis-concept-entity-types.md)概念信息
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)
* [角色与分层实体](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [使用模式改进预测](luis-concept-patterns.md)

## <a name="next-steps"></a>后续步骤

在本教程中，已创建了一个新的意向，并为源位置和目标位置的根据上下文学习的数据添加了示例陈述。 在训练并发布应用后，客户端应用程序可以使用该信息来创建包含相关信息的移动票证。

> [!div class="nextstepaction"] 
> [了解如何添加复合实体](luis-tutorial-composite-entity.md) 
