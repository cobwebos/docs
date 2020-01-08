---
title: 教程：列表实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 获取与项的预定义列表匹配的数据。 列表中的每个项可以有也是完全匹配的同义词
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 056c64657f42d56879928f518598206d45493f60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447787"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>教程：使用列表实体从言语中获取文本完全匹配的数据

本教程介绍如何获取与预定义的项列表完全匹配的数据。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 导入应用并使用现有意向
> * 添加列表实体
> * 训练、发布并查询应用以获取提取的数据

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>什么是列表实体？

列表实体是话语中的字词的文本完全匹配项。 列表中的每个项可以包含一个同义词列表。 需要完全匹配的项时，请使用列表实体。

对于此导入的 pizza（披萨）应用程序，请为不同类型的披萨饼皮创建一个列表实体。

出现以下情况时，列表实体非常适合此类数据：

* 数据值是已知的集。
* 此集不超出此实体类型的最大 LUIS [边界](luis-boundaries.md)。
* 话语中的文本是同义项或规范名称的完全匹配。 LUIS 不会使用除文本完全匹配项之外的列表。 仅仅有列表实体无法解析词干、复数形式和其他变体。 若要管理变体，请考虑使用带有可选文本语法的[模式](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)。

> [!CAUTION]
> 如果你不确定是要使用列表实体，还是要使用将短语列表用作描述符的机器学习实体，最佳且最灵活的做法是使用后者。 此方法可让 LUIS 学习和扩展要提取的数据值。

## <a name="import-example-json-and-add-utterances"></a>导入示例 .json 并添加言语

1.  下载并保存[应用 JSON 文件](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)。

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. 导入的应用包含 `OrderPizza` 意向。 请选择该意向，然后添加几个包含新饼皮类型的言语：

    |新言语|
    |--|--|
    |请订购小份的烤盘饼皮意式香肠披萨|
    |3 份薄皮夏威夷披萨|
    |请送 2 份填料饼皮披萨加面包棒|
    |一份厚皮披萨快餐|
    |一份深盘意式香肠披萨|

## <a name="crust-list-entity"></a>饼皮列表实体

既然 **OrderPizza** 意向包含具有饼皮类型的示例言语，LUIS 需要理解哪些单词代表饼皮类型。

主要名称和同义词的示例如下：

|规范名称|同义词|
|--|--|
|深盘|深<br>深盘饼皮<br>厚<br>厚饼皮|
|烤盘|普通<br>原配<br>一般<br>普通饼皮<br>原配饼皮<br>一般饼皮|
|填料|填料饼皮|
|薄|薄饼皮<br>纤细<br>纤细饼皮|

1. 在左侧面板中选择“实体”。 

1. 选择“+ 新建”。 

1. 在实体弹出对话框中，输入 `CrustList` 作为实体名称，输入“列表”作为实体类型。  选择“**下一页**”。

    > [!div class="mx-imgBorder"]
    > ![用于创建新实体的弹出对话框屏幕截图](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. 在“创建列表实体”页上，输入规范名称以及每个规范名称的同义词，然后选择“创建”。  

    > [!div class="mx-imgBorder"]
    > ![将项添加到列表实体的屏幕截图](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    将列表实体添加到 LUIS 应用时，无需[标记](label-entity-example-utterance.md)包含列表实体的文本。 这适用于所有意向中的所有言语。

## <a name="train-the-app-before-testing-or-publishing"></a>在测试或发布前训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>发布应用，从终结点进行查询

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>从终结点获取意向和实体预测结果

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. 转到地址中 URL 的末尾并输入以下话语：

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    最后一个查询字符串参数为 `query`，表示陈述**查询**。


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    已找到与文本完全匹配的饼皮类型，并已在 JSON 响应中返回。 客户端应用程序将使用此信息来处理订单。

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相关信息

* [列表实体](luis-concept-entity-types.md#list-entity)概念信息
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)
* [概念 - 实体](luis-concept-entity-types.md)
* [正则表达式实体的 JSON 参考](reference-entity-regular-expression.md?tabs=V3)
* [如何添加实体以提取数据](luis-how-to-add-entities.md)

## <a name="next-steps"></a>后续步骤
本教程添加了示例言语，然后创建了列表实体以从言语中提取确切的文本匹配项。 在训练并发布应用后，向终结点发出的查询识别了意向，并返回了提取的数据。

> [!div class="nextstepaction"]
> [添加带有角色的预生成实体](tutorial-entity-roles.md)

