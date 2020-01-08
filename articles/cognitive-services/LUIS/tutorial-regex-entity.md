---
title: 教程：正则表达式实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用正则表达式实体从言语中提取格式一致的数据。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 0ca6f2a67e01e4c604c2dcc8f8eaa9ffe8bad045
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381506"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>教程：从话语中获取格式正确的数据
在本教程中，我们将创建一个从言语中提取格式一致的数据的正则表达式实体。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 导入应用
> * 添加意向
> * 添加正则表达式实体
> * 训练、发布并查询应用以获取提取的数据

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>正则表达式实体

使用正则表达式实体从言语中提取适当格式的文本。 尽管话语的意向始终是使用机器学习确定的，但此特定实体类型不是机器学习的内容。 正则表达式实体的好处之一是可以通过[正则表达式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)来一致地表示任何文本。

`Send pizza delivery time to x123432`

此示例使用短代码发送文本消息。  此短代码为 5 位数或 6 位数代码，其前缀为 x，可以使用正则表达式 `x\d{5,6}` 来描述它。

将正则表达式实体添加到 LUIS 应用时，不需要使用正则表达式实体来[标记](label-entity-example-utterance.md)文本。 这适用于所有意向中的所有言语。

## <a name="import-example-json-to-begin-app"></a>导入示例 .json 以开始应用

1.  下载并保存[应用 JSON 文件](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>创建发送确认文本消息的意向

1. 选择“+ 创建”以创建一个新的意向，用于将发送确认文本的言语意向分类。 

1. 在弹出对话框中输入 `ConfirmationText`，然后选择“完成”。 

1. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |将披萨送餐时间发送到 x123432|
    |请用短信将时间发送到 x234567|
    |通知代码为 x23987|

    若要提取机器学习实体，应在各种言语中提供包含相应实体的示例，但如果使用此非机器学习实体，则变体并不重要。 只要文本与正则表达式匹配，就会提取文本。

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>将正则表达式实体用于格式正确的数据
创建要与文本编号匹配的正则表达式实体。 此正则表达式匹配文本，但忽略大小写和区域性变体。

1. 在左侧面板中选择“实体”。 

1. 在“实体列表”页上选择“+ 创建”。 

1. 在弹出对话框中，输入新实体名称 `ConfirmationTextRegEx`，选择“正则表达式”作为实体类型，然后选择“下一步”。  

    > [!div class="mx-imgBorder"]
    > ![针对正则表达式实体开始执行实体创建步骤](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. 在“创建正则表达式实体”中，输入 `x\d{5,6}` 作为“正则表达式”值，然后选择“创建”。   

    > [!div class="mx-imgBorder"]
    > ![输入正则表达式以从示例言语中提取数据](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. 在左侧菜单中选择“意向”，然后选择“ConfirmationText”意向查看言语中标记的正则表达式。  

    > [!div class="mx-imgBorder"]
    > ![查看示例言语中标记的正则表达式](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    由于该实体不是机器学习实体，因此在创建该实体后，它会立即应用到言语并显示在 LUIS 门户中。

## <a name="train-the-app-before-testing-or-publishing"></a>在测试或发布前训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>发布应用，从终结点进行查询

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>从终结点获取意向和实体预测结果

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. 转到地址中 URL 的末尾并输入以下话语：

    `Text my pizza delivery to x23456 x234567 x12345`

    最后一个查询字符串参数为 `query`，表示陈述**查询**。

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    LUIS 将使用正则表达式实体提取命名数据，从编程角度讲，这对于接收 JSON 响应的客户端应用程序而言会更有帮助。


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相关信息

* [概念 - 实体](luis-concept-entity-types.md)
* [正则表达式实体的 JSON 参考](reference-entity-regular-expression.md?tabs=V3)
* [如何添加实体以提取数据](luis-how-to-add-entities.md)

## <a name="next-steps"></a>后续步骤
本教程创建了新意向，添加了示例话语，然后创建了正则表达式实体以便从话语中提取格式良好的数据。 在训练并发布应用后，向终结点发出的查询识别了意向，并返回了提取的数据。

> [!div class="nextstepaction"]
> [了解列表实体](tutorial-list-entity.md)

