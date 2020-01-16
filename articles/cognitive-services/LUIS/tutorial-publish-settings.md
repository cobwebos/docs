---
title: 教程：发布设置 - LUIS
titleSuffix: Azure Cognitive Services
description: 本教程介绍如何更改发布设置以改善预测。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: diberry
ms.openlocfilehash: 2df32c20bebf4243f383a0cccd8f037721533602
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890385"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>教程：将情绪分析添加为发布设置

本教程介绍如何修改发布设置以提取情绪分析结果，然后查询 LUIS 终结点以查看用户言语的返回情绪。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 将情绪分析添加为发布设置
> * 从发布的终结点获取言语的情绪

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>情绪分析是一项发布设置

以下陈述显示了情绪的示例：

|情绪|Score|话语|
|:--|:--|:--|
|消极|0.01 |这披萨太难吃了。|
|积极|0.97 |这份奶酪披萨很棒。|

情绪分析是一种适用于所有话语的发布设置。 设置后，无需标记数据，应用就会返回言语的情绪。

由于情绪分析是一项发布设置，因此意向或实体页不会显示数据已标记。 可以在[交互式测试](luis-interactive-test.md#view-sentiment-results)窗格中或在终结点 URL 处进行测试时看到它。

## <a name="import-example-json-to-begin-app"></a>导入示例 .json 以开始应用

1.  下载并保存[应用 JSON 文件](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>训练应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>将应用配置为包含情绪分析

1. 从顶部菜单中选择“发布”  。 情绪分析是一项发布设置。

1. 依次选择“生产槽”、“更改设置”。  
1. 将“情绪分析”设置指定为“打开”。 

    ![开启“情绪分析”作为发布设置](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>从终结点获取话语的情绪

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 转到地址中 URL 的末尾并输入以下话语：

    `Deliver 2 of the best cheese pizzas ever!!!`

    最后一个查询字符串参数为 `query`，表示陈述**查询**。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含所提取的情绪分析的 `OrderPizza` 意向。

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
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
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    情绪分析结果是积极的，评分为 86%。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>相关信息

* 情绪分析由认知服务[文本分析](../Text-Analytics/index.yml)提供。 此功能仅限于文本分析[支持的语言](luis-language-support.md#languages-supported)。
* [如何训练](luis-how-to-train.md)
* [如何发布](luis-how-to-publish-app.md)
* [如何在 LUIS 门户中测试](luis-interactive-test.md)


## <a name="next-steps"></a>后续步骤
本教程将情绪分析添加为发布设置，以从整个话语中提取情绪值。

> [!div class="nextstepaction"]
> [在 HR 应用中审核终结点表述](luis-tutorial-review-endpoint-utterances.md)

