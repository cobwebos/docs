---
title: 教程：审核终结点话语 - LUIS
description: 在本教程中，通过验证或更正通过 LUIS HTTP 终结点收到的 LUIS 不确定的话语来改进应用预测。 某些陈述可能需要针对意向进行验证，而另一些陈述可能需要针对实体进行验证。
services: cognitive-services
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 32d43b36910c8fbfd60463f4062b6a00b9272fdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592570"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>教程：通过查看终结点话语来修复不确定的预测
本教程介绍如何通过验证或更正 LUIS 不确定的、通过 LUIS HTTPS 终结点收到的言语来改进应用预测。 在日常的计划性 LUIS 维护过程中，应该评审终结点言语。

此评审过程使 LUIS 能够了解你的应用域。 LUIS 将选择评审列表中显示的言语。 此列表具有以下特点：

* 特定于应用。
* 旨在改进应用的预测准确性。
* 应该定期进行审核。

可以通过审核终结点表述来验证或纠正表述的已预测意向。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 导入示例应用
> * 查看终结点话语
> * 训练和发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>下载适用于应用的 JSON 文件

下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true)。

## <a name="import-json-file-for-app"></a>导出适用于应用的 JSON 文件

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>训练应用以将实体更改应用于应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>发布应用以从 HTTP 终结点访问它

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>在终结点上添加言语

此应用中包含意向和实体，但不包含任何终结点使用情况信息。 若要使用终结点言语评审来改进应用，必须提供此终结点使用情况信息。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 转到地址栏中 URL 的末尾，将 _YOUR_QUERY_HERE_ 替换为下表中的言语。 对于每个言语，请提交言语并获取结果。 然后，将结尾的言语替换为下一个言语。

    |终结点言语|已调整意向|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    要审核的话语只有一个池，不管经常编辑哪个版本的话语，也不管在终结点上发布哪个版本的应用。

## <a name="review-endpoint-utterances"></a>查看终结点话语

评审终结点言语，使意向经过适当的调整。 尽管在所有版本中只需评审单个言语池，但适当调整意向的过程只会将示例言语添加到当前的活动模型。

1. 在门户的“生成”部分，从左侧导航栏中选择“评审终结点言语”。  列表会筛选出 **ApplyForJob** 意向。

    > [!div class="mx-imgBorder"]
    > ![左侧导航栏中“评审终结点言语”按钮的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    此话语 (`I'm looking for a job with Natural Language Processing`) 的意向不正确。

1.  若要调整此言语，请在言语行上，选择正确的“已调整意向”：`GetJobInformation`。 选择勾选标记将更改的言语添加到应用。

    > [!div class="mx-imgBorder"]
    > ![左侧导航栏中“评审终结点言语”按钮的屏幕截图](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    评审此意向中的剩余言语，并根据需要更正已调整的意向。 使用本教程中的初始言语表来查看已调整的意向。

    “评审终结点言语”列表应不再包含已更正的言语。 如果显示了其他言语，请继续在列表中更正已调整的意向，直到列表为空。

    对实体标签进行的任何更正是在调整意向后，通过“意向详细信息”页完成的。

1. 再次定型并发布应用。

## <a name="get-intent-prediction-from-endpoint"></a>从终结点获取意向预测

若要验证适当调整的示例言语是否已改进应用的预测，请尝试使用一个与已更正的言语接近的言语。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 转到地址栏中 URL 的末尾，将 _YOUR_QUERY_HERE_ 替换为 `Are there any natural language processing jobs in my department right now?`。

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
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

   适当调整不确定的言语后，将会预测正确的意向并返回**较高的评分**。

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>是否可以通过添加更多的表述来代替审核？
你可能会想，为何不添加更多的示例表述呢？ 审核终结点表述的目的是什么？ 在实际的 LUIS 应用中，终结点表述来自各个用户，其遣词造句方式与你并不相同。 如果同样的词汇和句式多次使用，则原始预测的百分比会更高。

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>为何顶级意向会包含在表述列表中？
某些终结点表述在审核列表中的预测分数会很高。 仍需审核并验证这些表述。 这些顶级意向之所以在列表中，是因为次高分数意向的分数与顶级意向的分数很接近。 你希望两个顶级意向之间存在大约 15% 的差异。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已审核了在终结点提交的、LUIS 不确定的表述。 在验证这些表述并将其作为示例表述移动到正确的意向中后，LUIS 将可以提高预测准确度。

> [!div class="nextstepaction"]
> [了解如何使用模式](luis-tutorial-pattern.md)
