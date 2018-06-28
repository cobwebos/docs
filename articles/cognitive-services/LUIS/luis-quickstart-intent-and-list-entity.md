---
title: 教程：创建 LUIS 应用以获取与所列数据完全匹配的文本 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和列表实体创建一个简单的 LUIS 应用，以提取本快速入门中的数据。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264821"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>教程：使用列表实体创建应用
在本教程中，我们将创建一个应用，用于演示如何获取与预定义列表匹配的数据。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解列表实体 
> * 使用 OrderDrinks 意向创建适用于饮料领域的新 LUIS 应用
> * 添加 _None_ 意向并添加示例陈述
> * 添加列表实体以从陈述中提取饮料项
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="purpose-of-the-list-entity"></a>列表实体的用途
此应用提取 `1 coke and 1 milk please` 等饮料订单，并返回饮料类型等数据。 饮料的**列表**实体会查找完全匹配的文本，并返回这些匹配项。 

当数据值是已知的集时，列表实体非常适合此类数据。 饮料名称可能有所不同（包括俚语和缩写），但这些名称不会经常变化。 

## <a name="app-intents"></a>应用意向
意向是用户需求的类别。 此应用有两个意向：OrderDrink 和 None。 [None](luis-concept-intent.md#none-intent-is-fallback-for-app) 意向是有针对性的，表示应用外部的任何属性。  

## <a name="list-entity-is-an-exact-text-match"></a>列表实体是确切的文本匹配项
实体的用途是查找陈述中的文本部分并将其分类。 使用[列表](luis-concept-entity-types.md)实体可以准确匹配单词或短语。  

对于此饮料应用，LUIS 会适当地提取饮料订单，以便能够创建和填充标准订单。 LUIS 允许陈述包含变体、缩写和俚语。 

用户提供的简单陈述示例包括：

```
2 glasses of milk
3 bottles of water
2 cokes
```

陈述的缩写或俚语版本包括：

```
5 milk
3 h2o
1 pop
```
 
列表实体将 `h2o` 与 water（水）匹配，将 `pop` 与 soft drink（软性饮料）匹配。  

## <a name="what-luis-does"></a>LUIS 的作用
在[终结点](https://aka.ms/luis-endpoint-apis)中识别、[提取](luis-concept-data-extraction.md#list-entity-data)并以 JSON 格式返回陈述的意向和实体后，LUIS 即告完成。 调用方应用程序或聊天机器人按照设计的任何方式提取该 JSON 响应并履行请求。 

## <a name="create-a-new-app"></a>创建新应用
1. 登录到 [LUIS][LUIS] 网站。 确保登录到需要发布 LUIS 终结点的[区域][LUIS-regions]。

2. 在 [LUIS][LUIS] 网站上，选择“创建新应用”。  

    ![创建新应用](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. 在弹出的对话框中，输入名称 `MyDrinklist`。 

    ![将应用命名为 MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. 过程完成后，应用会显示具有 **None** 意向的“意向”页。 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "“意向”页的屏幕截图")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>创建新意向

1. 在“意向”页上，选择“创建新意向”。 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "“意向”页的屏幕截图，其中已突出显示“创建新意向”按钮")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. 输入新意向名称 `OrderDrinks`。 每当用户想要订购饮料时，都应该选择此意向。

    创建意向会创建想要识别的主要信息类别。 为类别命名可让使用 LUIS 查询结果的其他任何应用程序使用该类别名称来查找相应的答案或采取相应的措施。 LUIS 不会回答这些问题，而只会识别以自然语言请求的信息类型。 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "创建新 OrderDrings 意向的屏幕截图")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. 将多个陈述添加到用户预期会请求的 `OrderDrinks` 意向，例如：

    | 示例陈述|
    |--|
    |Please send 2 cokes and a bottle of water to my room|
    |2 perriers with a twist of lime|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "在 OrderDrinks 意向页上输入陈述的屏幕截图")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>将陈述添加到 None 意向

LUIS 应用当前没有 **None** 意向的陈述。 它需要包含你不希望应用回答的陈述，因此，它必须在 **None** 意向中包含陈述。 请不要将此意向留空。 

1. 在左侧面板中选择“意向”。 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "左侧面板中选择意向链接的屏幕截图")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. 选择“None”意向。 添加用户可能会输入的、但与应用无关的三条陈述：

    | 示例陈述|
    |--|
    |Cancel!|
    |Good bye|
    |What is going on?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>何时预测 None 意向的陈述
在 LUIS 调用方应用程序（例如聊天机器人）中，当 LUIS 返回陈述的 **None** 意向时，机器人可以询问用户是否要结束对话。 如果用户不想要结束对话，机器人还能提供有关继续对话的更多指示。 

实体在 **None** 意向中工作。 如果评分最高的意向是 **None**，但提取的实体对聊天机器人而言有意义，则聊天机器人可以跟进以客户意向为重点的问题。 

## <a name="create-a-menu-entity-from-the-intent-page"></a>从“意向”页创建菜单实体
在两个意向中包含陈述后，LUIS 需要了解什么是饮料。 导航回到 `OrderDrinks` 意向，并遵循以下步骤标示（标记）陈述中的饮料：

1. 在左侧面板中选择“意向”，返回到 `OrderDrinks` 意向。

2. 在意向列表中选择 `OrderDrinks`。

3. 在陈述 `Please send 2 cokes and a bottle of water to my room` 中，选择单词 `water`。 此时会出现一个下拉菜单，其顶部显示了用于创建新实体的文本框。 在文本框中输入实体名称 `Drink`，然后在下拉菜单中选择“创建新实体”。 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "通过选择陈述中的单词创建新实体的屏幕截图")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. 在弹出窗口中，选择“列表”实体类型。 添加同义词 `h20`。 输入每个同义词后，按 Enter 键。 不要将 `perrier` 添加到同义词列表。 在下一步骤中，将添加此单词作为示例。 选择“完成”。

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "配置新实体的屏幕截图")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. 创建实体后，选择 water 的同义词来标记 water 的其他同义词，然后在下拉列表中选择 `Drink`。 遵循右侧的菜单操作，然后依次选择 `Set as synonym` 和 `water`。

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "标记包含现有实体的陈述的屏幕截图")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>在“实体”页中修改列表实体
饮料列表实体已创建，但不包含多个项和同义词。 如果你知道一些名词、缩写和俚语，在“实体”页上可以更快地填充列表。 

1. 在左侧面板中选择“实体”。

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "在左侧面板中选择“实体”的屏幕截图")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. 在实体列表中选择 `Drink`。

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "在实体列表中选择“饮料”实体的屏幕截图")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. 在文本框中输入 `Soda pop`，然后按 Enter。 此名词广泛适用于碳酸饮料。 对于此类饮料，每个地区都有俗称或俚语。

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "输入规范名称的屏幕截图")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. 在 `Soda pop` 所在的同一行上，输入如下所示的同义词： 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    同义词可以包含短语、标点、所有格和复数形式。 由于列表实体是是准确的文本匹配项（大小写除外），因此同义词需要包含每个变体。 在查询日志中或者通过查看终结点命中次数了解更多的变体后，可以扩展该列表。 

    为了示例简短，本文中的同义词较少。 生产级 LUIS 应用包含许多的同义词，并会定期接受审查和扩展。 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "添加同义词的屏幕截图")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![训练应用](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "选择发布按钮的屏幕截图")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. 选择“生产”槽和“发布”按钮。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "选择“发布到生产槽”按钮的屏幕截图")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的陈述查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "“发布”页上的终结点 URL 屏幕截图")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. 将光标定位到地址中 URL 的末尾，并输入 `2 cokes and 3 waters`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含两个饮料类型（`cokes` 和 `waters`）的 `OrderDrinks` 意向。

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>列表实体中的自然语言处理在何处发生？ 
由于列表实体是准确的文本匹配项，因此它不依赖于自然语言处理（或机器学习）。 LUIS 确实使用自然语言处理（或机器学习）来选择评分最高的正确意向。 此外，陈述中可能混合多个实体，甚至多个实体类型。 将为应用中的所有实体（包括自然语言处理（或机器学习的）实体，例如**简单**实体）处理每个陈述。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用只包含两个意向和一个列表实体，识别了自然语言查询意向，并返回了提取的数据。 

现在，聊天机器人获得了足够的信息，可确定主要操作 `OrderDrinks`，以及从“饮料”列表实体订购了哪些类型的饮料。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何添加正则表达式实体](luis-quickstart-intents-regex-entity.md)

添加**数字**[预生成实体](luis-how-to-add-entities.md#add-prebuilt-entity)以提取数字。 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
