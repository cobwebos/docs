---
title: 教程：预测意向 - LUIS
description: 创建一个自定义应用，以便根据本教程中的言语（文本）预测用户的意向。
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: c76273d7c180928d25be70e0abd7abf26c90b44a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588920"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>教程：生成 LUIS 应用以确定用户意向

在本教程中，创建一个自定义应用，以便根据言语（文本）预测用户的意向。

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 创建新应用
> * 创建意向
> * 添加示例话语
> * 训练应用
> * 发布应用
> * 从终结点获取意向预测

## <a name="user-intentions-as-intents"></a>将用户意向用作意向

此应用的目的是确定聊天性的自然语言文本的意图：

`I'd like to order a veggie pizza with a salad on the side.`

这些意图可以归类为**意向**。

|Intent|目的|
|--|--|
|`ModifyOrder`|确定用户的披萨订单。|
|`Greeting`|开始机器人对话。|
|`ConfirmOrder`|确认披萨订单。|
|`None`|确定用户是否正在询问 LUIS 应用不应回答的问题。 此意向如果作为应用创建的一部分提供，则无法删除。 |

## <a name="create-a-new-app"></a>创建新应用

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>创建新意向

意向用于根据用户的意图（根据自然语言文本确定）对用户言语进行分类。

要对言语进行分类，意向需要使用应根据该意向分类的用户言语的示例。

1. 在“意向”页面的“生成”部分中，选择“+ 创建”以创建新意向  。 输入新意向名称 `OrderPizza`，然后选择“完成”。

    在用户想要订购披萨时，预测 `OrderPizza` 意向。

1. 将多个示例话语添加到你预期用户会请求的此意向：

    |`OrderPizza` 示例言语|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![在 LUIS 门户的意向页上添加示例言语的屏幕截图](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    可通过提供示例话语来训练 LUIS，让其了解应该根据哪些类型的话语预测出此意向。 以下是几个正面示例。 其他所有意向中的言语均被视为该意向的负面示例。

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>创建其余意向

1. 创建 `Greeting` 意向，并添加以下示例言语。 这是用于确定用户是否开始新的披萨订单会话的意向。

    |`Greeting` 示例言语|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. 创建 `Confirm` 意向，并添加以下示例言语。 这是一个意向，用于确定用户是否已完成订购并接受订单详细信息。

    |`Confirm` 示例言语|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None 意向示例言语

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>发布应用

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>获取意向预测

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 转到地址栏中 URL 的末尾，输入：

    `get a medium vegetarian pizza for delivery`

    这与示例示例言语并不完全相同，因此，要查看 LUIS 是否可以了解通过此意向预测的内容。

    最后一个查询字符串参数为 `query`，表示陈述查询 (**q**uery)。 此话语不同于任何示例话语。 它非常适合用于测试，测试结果应返回 `OrderPizza` 意向（评分最高的意向）。

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    实体数组为空，因为此应用当前没有任何实体（言语中可供提取的数据单元）。

    JSON 结果中标识了评分最高的意向作为 **`prediction.topIntent`** 属性。 所有评分介于 1 和 0 之间，评分越接近 1 越好。

1. 更改 URL 查询参数以符合“问候语”意向：

    `Howdy`

    这与示例示例言语并不完全相同，因此，要查看 LUIS 是否可以了解通过此意向预测的内容。

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    此预测的置信度分数为 44%。 若要提高置信度分数，请添加 15 到 30 个示例言语。

## <a name="client-application-next-steps"></a>客户端应用程序后续步骤

本教程创建了 LUIS 应用、创建了意向、向每个意向添加了示例言语、向 None 意向添加了示例言语，并在终结点上进行了训练、发布和测试操作。 这些是生成 LUIS 模型的基本步骤。

LUIS 在返回 JSON 响应后，就已经完成了此请求。 LUIS 不提供用户话语的应答，只会识别以自然语言请求的信息类型。 会话跟进由客户端应用程序（如 Azure 机器人）提供。


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相关信息

* [实体类型](luis-concept-entity-types.md)
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)
* [Azure 机器人](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将可分解实体添加到此应用](tutorial-machine-learned-entity.md)
