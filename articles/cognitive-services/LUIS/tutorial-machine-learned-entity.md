---
title: 教程：使用机器学习实体提取结构化数据 - LUIS
description: 使用机器学习实体从言语中提取结构化数据。 若要提高提取准确性，请添加包含功能的子实体。
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: eb9761a3d3a98a3318fe0adc6fa170652639a9a1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045597"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>教程：在语言理解 (LUIS) 中使用机器学习实体从用户言语中提取结构化数据

在本教程中，使用机器学习实体从言语中提取结构化数据。

机器学习实体通过为子实体提供[特性](luis-concept-feature.md)来支持[模型分解概念](luis-concept-model.md#v3-authoring-model-decomposition)。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 导入示例应用
> * 添加机器学习实体
> * 添加子实体和功能
> * 训练、测试、发布应用
> * 从终结点获取实体预测

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>为什么使用机器学习实体？

本教程添加了机器学习实体，用于从用户言语中提取数据。

该实体定义要从言语中提取的数据。 这包括为数据提供名称、类型（如果可能）、任何数据的解析（如果有多义性）以及组成数据的确切文本。

若要定义数据，需要：
* 创建实体
* 在示例言语中标记表示实体的文本。 这些标记的示例告知 LUIS 什么是实体以及在言语中何处可以找到实体。

## <a name="entity-decomposability-is-important"></a>实体可分解性非常重要

实体可分解性对于使用实体进行意向预测和数据提取十分重要。

从机器学习实体开始操作，它是数据提取的起始、顶层实体。 然后，将实体分解为子实体。

刚开始创建应用时，你可能不知道需要多详细的实体，最佳做法是从机器学习实体开始，随着应用的成熟用子实体进行分解。

在本教程中，你将创建进行机器学习实体来表示披萨应用的订单。 实体将提取与订单相关的文本，从而提炼出大小和数量信息。

言语 `Please deliver one large cheese pizza to me` 应提取 `one large cheese pizza` 作为订单，然后提取 `1` 的数量和 `large` 的大小。

## <a name="download-json-file-for-app"></a>下载适用于应用的 JSON 文件

下载并保存[应用 JSON 文件](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)。

## <a name="import-json-file-for-app"></a>导出适用于应用的 JSON 文件

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>创建机器学习实体

为了提取披萨订单的详细信息，创建顶层的机器学习 `Order` 实体。

1. 在“意向”页上，选择“OrderPizza”意向 。

1. 在示例言语列表中，选择以下言语。

    |订单示例言语|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    在最左侧文本 `pickup` (#1) 之前开始选择，然后移到最右侧文本 `anchovies`（#2 - 这将结束标记过程）的上方。 此时将显示一个弹出菜单。 在弹出框中，输入实体的名称作为 `Order` (#3)。 然后从列表选择 `Order Create new entity` (#4)。

    ![标记完整订单文本的开头和结尾](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > 实体不一定是整个言语。 在此特定示例中，`pickup` 指示如何接收订单。 从概念角度来看，`pickup` 应是订单的已标记实体的一部分。

1. 在“选择实体类型”框中，选择“添加结构”，然后选择“下一步”。 结构是添加子实体（例如大小和数量）所必需的。

    ![向实体添加结构](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. 在“添加子实体(可选)”框中，选择 `Order` 行上的 +，将 `Size` 和 `Quantity` 添加为子实体，然后选择“创建”  。

    > [!div class="mx-imgBorder"]
    > ![向实体添加结构](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>编辑子实体以改进提取

上述步骤可以创建实体和子实体。 若要改进提取，请向子实体添加特性。

### <a name="improve-size-extraction-with-phrase-list"></a>改进短语列表中的尺寸提取

1. 从左侧菜单中选择“实体”，然后选择“订单”实体 。

1. 在“架构和特性”选项卡上，选择“尺寸”子实体，然后选择“+ 添加特性”  。

1. 在下拉菜单中选择“新建短语列表”。

1. 在“创建新短语列表”框中，输入名称 `SizePhraselist`，然后输入 `small`、`medium` 和 `large` 值。 当填充“建议”框时，选择 `extra large` 和 `xl`。 选择“创建”来创建新的短语列表。

    此短语列表功能通过提供示例字词，帮助 `Size` 子实体查找与大小相关的字词。 此短语列表不需要包含每个尺寸词，而应当包含预期指示尺寸的字词。

### <a name="add-sizelist-entity"></a>添加 SizeList 实体

添加客户端应用程序可以识别的已知尺寸列表也将有助于提取。

1. 从左侧菜单中选择“实体”，然后选择“+ 创建” 。

1. 将实体名称设置为 `SizeListentity`，并将“类型”设置为“列表”，以便在与前一个部分中创建的 `SizePhraselist` 进行比较时轻松识别它。

1. 添加客户端应用程序预期的尺寸：`Small`、`Medium`、`Large` 和 `XLarge`，然后为每个尺寸添加同义词。 同义词应该是用户在聊天机器人中输入的词语。 实体与规范化值或同义词完全匹配时，将提取出该实体以及列表实体。

    |规范化值|同义词|
    |--|--|
    |小型|sm、sml、tiny、smallest|
    |中型|md、mdm、regular、average、middle|
    |大型|lg、lrg、big|
    |XLarge|xl、biggest、giant|


    > [!div class="mx-imgBorder"]
    > ![向实体添加结构](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>添加 SizeList 实体的特性

1. 从左侧菜单中选择“实体”，返回到实体列表。

1. 从实体列表中选择“订单”。

1. 在“架构和特性”选项卡上，选择“大小”实体，然后选择“+ 添加特性”  。

1. 从下拉列表中选择“@ SizeListentity”。

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>添加预生成数字实体

添加预生成的数字实体也将有助于提取。

1. 从左侧菜单中选择“实体”，然后选择“添加预生成实体” 。

1. 从列表中选择“数字”，然后选择“完成” 。

1. 从左侧菜单中选择“实体”，返回到实体列表。

### <a name="add-feature-of-prebuilt-number-entity"></a>为预生成的数字实体添加特性

1. 从实体列表中选择“订单”。

1. 在“架构和特性”选项卡上，选择“数量”实体，然后选择“+ 添加特性”  。

1. 从下拉列表中选择“@ number”。

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>配置所需特性

在“订单”实体的实体详细信息页上，选择星号 `*`，其表示“@ SizeList”特性和“@ number”特性  。 星号显示在与特性名称相同的标签中。

> [!div class="mx-imgBorder"]
> ![向实体添加结构](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>标签示例言语

创建了机器学习实体，且子实体具有特性。 为完成对提取的改进，需要使用子实体标记示例言语。

1. 在左侧导航栏中选择“意向”，然后选择“OrderPizza”意向 。

1. 若要打开“实体调色板”，请在上下文工具栏中选择 @ 符号 。

1. 选择调色板中的每个实体行，然后使用调色板光标选择每个示例言语中的实体。 完成操作后，实体列表应如下图所示。

    > [!div class="mx-imgBorder"]
    > ![配置所需特性的部分屏幕截图](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>训练应用

若要训练应用，请选择“训练”。 训练会将更改（如新实体和已标记的言语）应用于活动模型。

## <a name="add-a-new-example-utterance"></a>添加新的示例言语

1. 进行训练后，添加新的示例言语到 `OrderPizza` 意向，以查看 LUIS 对机器习得实体的理解程度。

    |订单示例言语|
    |--|
    |`I need a large pepperoni pizza`|

    整个顶部实体 `Order` 已进行标记，`Size` 子实体也标有虚线。

    > [!div class="mx-imgBorder"]
    > ![通过实体预测的新示例言语的部分屏幕截图](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    虚线表示基于当前训练后应用的预测。

1. 若要将预测更改为标记实体，请选择同一行中的复选标记。

    > [!div class="mx-imgBorder"]
    > ![通过实体预测的新示例言语的部分屏幕截图](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    此时，机器学习实体有效，因为它可以在新的示例言语中找到该实体。 添加示例言语时，如果未正确预测实体，请标记实体和子实体。 如果正确预测了实体，请确保确认预测。


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>训练应用以将实体更改应用于应用

选择“训练”以使用这一新言语训练应用。

此时，订单有一些可提取的详细信息（大小、数量和订单总计文本）。 还可以进一步优化 `Order` 实体，如披萨浇汁、酥皮类型和配菜订单。 其中每一项应作为 `Order` 实体的子实体创建。

## <a name="test-the-app-to-validate-the-changes"></a>测试应用以验证更改

使用交互式“测试”面板测试应用。 在此过程中，可以输入新的言语，然后查看预测结果，以查看活动的和经过训练的应用的工作情况。 意向预测应相当有把握（超过 60%），实体提取应至少选取 `Order` 实体。 订单实体的详细信息可能缺失，因为这几个言语不足以处理所有情况。

1. 选择顶部导航栏中的“测试”。
1. 输入言语 `2 small cheese pizzas for pickup`，并选择 Enter。 活动模型通过超过 60% 的置信度预测正确意向。


1. 选择“检查”以查看实体预测。

    > [!div class="mx-imgBorder"]
    > ![在交互式测试面板中查看实体预测的部分屏幕截图。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>发布应用以从 HTTP 终结点访问它

若要在聊天机器人或其他客户端应用程序中接收 LUIS 预测，需要将应用发布到终结点。

1. 在右上方的导航栏中选择“发布”。

    ![右上方菜单中的“LUIS 发布到终结点”按钮的屏幕截图](./media/howto-publish/publish-button.png)

1. 依次选择“生产”槽、“更改设置”、“情绪分析”、“完成”   。

    > [!div class="mx-imgBorder"]
    > ![“LUIS 发布到终结点”的屏幕截图](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. 在通知中选择“访问终结点 URL”链接，转到“Azure 资源”页。  终结点 URL 作为“示例查询”列出。

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>从 HTTP 终结点获取意向和实体预测结果

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 在地址栏中转到 URL 的末尾，将 YOUR_QUERY_HERE 替换为在交互式测试面板中输入的相同查询。

    `2 small cheese pizzas for pickup`

    最后一个查询字符串参数为 `query`，表示陈述**查询**。

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相关信息

* [教程 - 意向](luis-quickstart-intents-only.md)
* [概念 - 实体](luis-concept-entity-types.md)概念信息
* [概念 - 功能](luis-concept-feature.md)概念信息
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)

## <a name="next-steps"></a>后续步骤

在本教程中，应用使用机器学习实体来查找用户言语的意向，并从该言语中提取详细信息。 使用机器学习实体可以分解实体的详细信息。

> [!div class="nextstepaction"]
> [添加预生成的 keyphrase 实体](luis-quickstart-intent-and-key-phrase.md)
