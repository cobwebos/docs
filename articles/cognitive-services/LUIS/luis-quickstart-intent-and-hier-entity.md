---
title: 教程 5：父/子关系 - 根据上下文学习的数据的 LUIS 分层实体
titleSuffix: Azure Cognitive Services
description: 基于上下文查找相关的数据片段。 例如，对于从一个建筑物和办公室到另一个建筑物和办公室的物理移动，源位置和目标位置是相关的。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 92b6327cbb97ed871cd4b10977bcd73a81494e20
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042119"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>教程 5：提取与上下文相关的数据
在本教程中，基于上下文查找相关的数据片段。 例如，对于从一个建筑物和办公室到另一个建筑物和办公室的物理移动，源位置和目标位置是相关的。 若要生成工作订单，可能同时需要这两个数据片段，并且它们彼此相关。  

此应用用于确定在何处将员工从原始位置（大楼和办公室）移到目标位置（大楼和办公室）。 它使用分层实体来确定陈述中的位置。 **分层**实体的用途是根据上下文查找陈述中的相关数据。 

分层实体非常适合此类数据，因为这两个数据片段：

* 是简单实体。
* 在使用陈述的情况下彼此相关。
* 使用特别选择的词汇来指示每个位置。 这些词汇的示例包括：from/to（从/到）、leaving/headed to（离开/前往）、away from/toward（离开/前往）。
* 两个位置往往在同一个陈述中。 
* 需要由客户端应用作为一个信息单元进行分组和处理。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用现有的教程应用
> * 添加意向 
> * 添加包含出发地和目的地子级的位置分层实体
> * 训练
> * 发布
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用
继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分中，在“版本”选项卡上，克隆版本并将其命名为 `hier`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。 

## <a name="remove-prebuilt-number-entity-from-app"></a>从应用中删除预生成的数字实体
若要查看整个陈述并标记分层子级，请暂时删除预生成的数字实体。

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 在左侧菜单中选择“实体”。

3. 选择列表中数字实体右侧的省略号 ***(...)*** 按钮。 选择“删除”。 

## <a name="add-utterances-to-moveemployee-intent"></a>将表述添加到 MoveEmployee 意向

1. 在左侧菜单中选择“意向”。

2. 从意向列表中选择“MoveEmployee”。

3. 添加以下示例陈述：

    |示例陈述|
    |--|
    |将 John W. Smith 移**到** a-2345|
    |要求 Jill Jones 转**到** b-3499|
    |组织 x23456 **从** hh-2345 移**到** e-0234|
    |开始文书工作，将 x12345 设置为**离开** a-3459，**前往** f-34567|
    |让 425-555-0000 **离开** g-2323，**前往** hh-2345|

    [ ![LUIS 的屏幕截图，在 MoveEmployee 意向中有新陈述](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    在[列表实体](luis-quickstart-intent-and-list-entity.md)教程中，通过姓名、电子邮件地址、电话分机、移动电话号码或美国联邦社会安全号码指定了某个员工。 这些员工编号用在陈述中。 前面的示例陈述包括了用于表示原始位置和目标位置（以粗体标记）的不同方式。 一些陈述特意只包含目标位置。 这有助于 LUIS 了解在不指定原始位置的情况下，如何在陈述中放置这些位置。     

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>创建位置实体
LUIS 需要通过在陈述中标记原始位置和目标位置来了解什么是位置。 如需在令牌（原始）视图中查看陈述，请在标记为“实体视图”的陈述上方的栏中选择切换。 切换开关以后，此控件会被标记为“令牌视图”。

考虑以下陈述：

```JSON
mv Jill Jones from a-2349 to b-1298
```

该陈述中指定了两个位置：`a-2349` 和 `b-1298`。 假定字母对应于大楼名称，数字表示该大楼中的办公室。 分组时，可以将它们都作为分层实体 `Locations` 的子级，因为需要从陈述中提取这两种数据片段来完成客户端应用程序中的请求，而且这两种数据片段彼此相关。 
 
如果只存在分层实体的一个子级（出发地或目的地），仍会提取该子级。 不需要找到所有子级即可提取一个或一些子级。 

1. 在陈述 `Displace 425-555-0000 away from g-2323 toward hh-2345` 中，选择单词 `g-2323`。 此时会出现下拉菜单，其顶部有一个文本框。 在文本框中输入实体名称 `Locations`，然后在下拉菜单中选择“创建新实体”。 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "在意向页上创建新实体的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. 在弹出窗口中，选择包含 `Origin` 和 `Destination` 子实体的“分层”实体类型。 选择“完成”。

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "用于创建新位置实体的弹出对话框的屏幕截图")

3. `g-2323` 的标签已标记为 `Locations`，因为 LUIS 不知道名词是出发地还是目的地，或者两者都不是。 依次选择 `g-2323`、“位置”，遵循菜单操作，然后选择 `Origin`。

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "实体的屏幕截图，该实体标记用于更改位置实体子级的弹出对话框")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. 标记所有其他陈述中的其他位置，方法是：选择陈述中的大楼和办公室，然后选择“位置”，再遵循右侧的菜单选择 `Origin` 或 `Destination`。 所有位置都标记以后，“令牌视图”中的陈述开始显示出某种模式。 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "在陈述中标记的“位置”实体的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>向应用添加预生成的数字实体
将预生成的数字实体添加回应用程序。

1. 在左侧导航菜单中选择“实体”。

2. 选择“管理预生成的实体”按钮。

3. 从预生成的实体的列表中选择“数字”，然后选择“完成”。

    ![在“预生成的实体”对话框中选择的数字的屏幕截图](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的话语查询终结点

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. 将光标定位到地址栏中 URL 的末尾，并输入 `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`。 最后一个查询字符串参数为 `q`，表示陈述**查询**。 此话语不同于标记的任何话语，因此，它非常适合用于测试，测试结果应返回包含所提取的分层实体的 `MoveEmployee` 意向。

    ```JSON
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    预测了正确的意向，并且实体数组在对应的 **entity** 属性中同时具有源和目标值。
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>是否可以为每个位置使用正则表达式？
是的，可以创建包含原始位置和目标位置角色的正则表达式实体，然后将其用在某个模式中。

此示例中的位置（例如 `a-1234`）遵循特定的格式，即以一到两个字母开头，后接破折号，然后是一系列数字（4 到 5 个）。 可以将该数据描述为一个正则表达式实体，每个位置有一个角色。 角色仅适用于模式。 可以根据这些陈述创建模式，然后针对位置格式创建一个正则表达式，再将其添加到模式。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
在本教程中，已创建了一个新的意向，并为源位置和目标位置的根据上下文学习的数据添加了示例陈述。 在训练并发布应用后，客户端应用程序可以使用该信息来创建包含相关信息的移动票证。

> [!div class="nextstepaction"] 
> [了解如何添加复合实体](luis-tutorial-composite-entity.md) 