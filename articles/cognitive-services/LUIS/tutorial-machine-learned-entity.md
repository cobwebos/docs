---
title: 教程：使用机器学习实体提取结构化数据 - LUIS
description: 使用机器学习实体从言语中提取结构化数据。 若要提高提取准确性，请添加包含功能的子实体。
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: d1bc8fc6aac52e264cb4352ca05f9df45ccfc50e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588864"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>教程：在语言理解 (LUIS) 中使用机器习得实体从用户言语中提取结构化数据

在本教程中，使用机器学习实体从言语中提取结构化数据。

机器学习实体通过为子实体提供[功能](luis-concept-feature.md)，来支持[模型分解概念](luis-concept-model.md#v3-authoring-model-decomposition)。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 导入示例应用
> * 添加机器学习实体
> * 添加子实体和功能
> * 训练、测试、发布应用
> * 从终结点获取实体预测

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>为什么使用机器学习实体？

本教程添加了机器学习实体，用于从用户言语中提取数据。

该实体定义要从言语中提取的数据。 这包括为数据提供名称、类型（如果可能）、任何数据的解析（如果有多义性）以及组成数据的确切文本。

若要定义数据，需要：
* 创建实体
* 在示例言语中标记表示实体的文本。 这些标记的示例告知 LUIS 什么是实体以及在言语中何处可以找到实体。

## <a name="entity-decomposability-is-important"></a>实体可分解性非常重要

实体可分解性对于使用实体进行意向预测和数据提取十分重要。

从机器学习实体（该实体是数据提取的开始和顶层实体）开始操作。 然后，将实体分解为子实体。

当你开始使用应用时，你可能不知道你需要实体的详细程度，最佳做法是从机器学习实体开始，然后随着应用的成熟，分解为子实体。

在本教程中，你将创建一个进行机器学习的实体来表示披萨应用的订单。 实体将提取与订单相关的文本，从而提炼出大小和数量信息。

言语 `Please deliver one large cheese pizza to me` 应提取 `one large cheese pizza` 作为订单，然后提取 `1` 的数量和 `large` 的大小。

## <a name="download-json-file-for-app"></a>下载适用于应用的 JSON 文件

下载并保存[应用 JSON 文件](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)。

## <a name="import-json-file-for-app"></a>导出适用于应用的 JSON 文件

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>在示例言语中将文本标记为实体

若要提取有关披萨订单的详细信息，请创建顶层的机器学习 `Order` 实体。

1. 在“意向”页上，选择“OrderPizza”意向 。

1. 在示例言语列表中，选择以下言语。

    |订单示例言语|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    在最左侧文本 `pickup` (#1) 之前开始选择，然后移到最右侧文本 `anchovies`（#2 - 这将结束标记过程）的上方。 此时将显示一个弹出菜单。 在弹出框中，输入实体的名称作为 `Order` (#3)。 然后从列表选择 `Order - Create new entity` (#4)。

    ![标记完整订单文本的开头和结尾](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > 实体不一定是整个言语。 在此特定示例中，`pickup` 指示如何接收订单。 从概念角度来看，`pickup` 应是订单的已标记实体的一部分。

1. 在“选择实体类型”框中，选择“添加结构”，然后选择“下一步”。 结构是添加子实体（例如大小和数量）所必需的。

    ![向实体添加结构](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. 在“创建机器学习实体”框中，在“结构”框中添加 `Size`，然后选择 Enter。
1. 若要添加功能，请在“功能”区域中选择 `+`，然后选择“创建新短语列表”。

1. 在“创建新短语列表”框中，输入名称 `SizeFeature`，然后输入 `small`、`medium` 和 `large` 值。 当填充“建议”框时，选择 `extra large` 和 `xl`。 选择“完成”以创建新的短语列表。

    此短语列表功能通过提供示例字词，帮助 `Size` 子实体查找与大小相关的字词。 此列表不需要包含每个大小的词语，而应当包含预期指示大小的词语。

    ![为大小子实体创建功能](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. 在“创建机器学习实体”窗口中，选择“创建”以完成创建 `Size` 子实体。

    已创建具有 `Size` 实体的 `Order` 实体，但只有 `Order` 实体应用于言语。 你需要在查询言语中标记 `Size` 实体文本。

1. 在同一个示例言语中，通过选择词语后从下拉列表中选择“大小”实体，标记 `large` 的“大小”子实体。

    ![标记言语中文本的大小实体。](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    文本下的线是实线，因为标记和预测均匹配，这是因为你显式标记了文本。

1. 标记剩余言语中的 `Order` 实体以及大小实体。 文本中的方括号指示已标记的 `Order` 实体和其中的 `Size` 实体。

    |订单示例言语|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![在所有剩余示例言语中生成实体和子实体。](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > 如何处理暗示数据（如字母 `a` 暗示单个披萨）？ 或者缺少 `pickup` 和 `delivery` 来指出需要披萨的地点？ 或者缺少一个大小来指出默认大小：小或者大？ 考虑将暗示数据处理视为客户端应用程序中的业务规则的一部分，而不是 LUIS 的一部分，或将其视为除 LUIS 以外的一部分。

1. 若要训练应用，请选择“训练”。 训练会将更改（如新实体和已标记的言语）应用于活动模型。

1. 进行训练后，添加新的示例言语到意向，以查看 LUIS 对机器习得实体的理解程度。

    |订单示例言语|
    |--|
    |`pickup XL meat lovers pizza`|

    整个顶部实体 `Order` 已进行标记，`Size` 子实体也标有虚线。

    ![通过实体预测的新示例言语](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    虚线表示预测。

1. 若要将预测更改为已标记实体，选择该行，然后选择“确认实体预测”。

    ![通过选择“确认实体预测”接受预测。](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    此时，机器学习实体正在工作，因为它可以在新的示例言语中找到该实体。 添加示例言语时，如果未正确预测实体，请标记实体和子实体。 如果正确预测了实体，请确保确认预测。


<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="add-subentity-with-feature-of-prebuilt-entity"></a>添加具有预生成实体功能的子实体

订单信息还应包含订单中的项的数量，例如多少个披萨。 若要提取此数据，需要将一个新的机器学习子实体添加到 `Order`，该子实体需要预生成数字的必需功能。 通过将预生成实体的功能用于预生成数字，实体将查找并提取数字，无论文本是数字 `2` 还是文本 `two`。

## <a name="add-prebuilt-number-entity-to-app"></a>向应用添加预生成的数字实体
订单信息还应包含订单中的项的数量，例如，多少个披萨。 若要提取此数据，需要将一个新的机器学习子组件添加到 `Order`，该组件需要预生成数字的必需功能。 通过将实体限制到预生成数字，实体将查找并提取数字，无论文本是数字 `2` 还是文本 `two`。

首先将预生成数字实体添加到应用。

1. 从左侧菜单中选择“实体”，然后选择“+ 添加预生成实体”。

1. 在“添加预生成实体”框中，搜索并选择“数字”，然后选择“完成”"。

    ![添加预生成实体](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    预生成的实体已添加到应用，但还不是一个功能。

## <a name="create-subentity-entity-with-required-feature-to-help-extract-data"></a>创建具有必需功能的子实体，以帮助提取数据

`Order` 实体应有一个 `Quantity` 子实体，用于确定订单中的项的数量。 此数量应使用预生成数字的必需功能，使客户端应用程序可按名称立即使用提取的数据。

必需功能作为文本匹配应用，该匹配项具有完全匹配（如列表实体）或通过正则表达式匹配（如正则表达式实体或预生成的实体）。

通过将非计算机学习实体作为功能，将只提取匹配的文本。

1. 选择“实体”，然后选择 `Order` 实体。
1. 选择“+ 添加实体”，输入名称 `Quantity`，然后选择 Enter 以将新子实体添加到 `Order` 实体。
1. 显示成功通知消息后，在“高级选项”中选择“约束”铅笔图标。
1. 在下拉列表中，选择预生成的数字。

    ![创建具有预生成数字作为约束的数量实体。](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    当文本与预生成数字实体匹配时，应用 `Quantity` 实体。

    已创建具有必需功能的实体，但该实体尚未应用于示例言语。

    > [!NOTE]
    > 子实体可以嵌套在最多 5 个级别的子实体中。 尽管本文中未显示这一点，但可从门户和 API 获得。

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>用于教授 LUIS 关于实体的信息的标签示例言语

1. 在左侧导航栏中选择“意向”，然后选择“OrderPizza”意向 。 以下言语中的三个数字均被标记，但在 `Order` 实体行的下方可见。 这一较低级别表示找到实体，但不会将其视为 `Order` 实体的一部分。

    ![找到预生成数字，但不会将其视为与订单实体分离。](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. 通过在示例言语中选择 `2` 后从列表中选择 `Quantity`，使用 `Quantity` 实体来标记数字。 在同一示例言语中标记 `6` 和 `1`。

    ![标记具有数量实体的文本。](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>训练应用以将实体更改应用于应用

选择“训练”以使用这些新言语训练应用。 训练后，将在 `Order` 实体中正确预测 `Quantity` 子实体。 此正确的预测以实线表示。

![训练应用，然后查看示例言语。](media/tutorial-machine-learned-entity/trained-example-utterances.png)

此时，订单有一些可提取的详细信息（大小、数量和订单总计文本）。 还可以进一步优化 `Order` 实体，如披萨浇汁、酥皮类型和配菜订单。 其中每一项应作为 `Order` 实体的子实体创建。

## <a name="test-the-app-to-validate-the-changes"></a>测试应用以验证更改

使用交互式“测试”面板测试应用。 在此过程中，可以输入新的言语，然后查看预测结果，以查看活动的和经过训练的应用的工作情况。 意向预测应相当有把握（超过 70%），实体提取应至少选取 `Order` 实体。 订单实体的详细信息可能缺失，因为 5 个言语不足以处理每个案例。

1. 选择顶部导航栏中的“测试”。
1. 输入言语 `deliver a medium veggie pizza`，并选择 Enter。 活动模型通过超过 70% 的置信度预测正确意向。

    ![输入新的言语来测试意向。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. 选择“检查”以查看实体预测。

    ![在交互式测试面板中查看实体预测。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    已正确标识大小。 请记住，`OrderPizza` 意向中的示例言语没有 `medium` 作为大小的示例，但是使用包含 medium 的 `SizeFeature` 短语列表的功能。

    未正确预测数量。 如果 LUIS 预测中未返回任何大小，可以在客户端应用程序中通过将默认大小设置为一 (1) 来解决此问题。

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>发布应用以从 HTTP 终结点访问它

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>从 HTTP 终结点获取意向和实体预测结果

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 在地址栏中转到 URL 的末尾，将 YOUR_QUERY_HERE 替换为在交互式测试面板中输入的相同查询。

    `deliver a medium veggie pizza`

    最后一个查询字符串参数为 `query`，表示陈述**查询**。

    ```json
    {
        "query": "deliver a medium veggie pizza",
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
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
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
