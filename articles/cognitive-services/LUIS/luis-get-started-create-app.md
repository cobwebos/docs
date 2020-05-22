---
title: 快速入门：创建应用 - LUIS
description: 本快速入门演示如何创建使用预生成域 `HomeAutomation` 打开和关闭灯光和设备的 LUIS 应用。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 186a60a7fd8315d68718ceedd3b5cadb4d3645e8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589110"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>快速入门：使用预构建的家庭自动化应用

在本快速入门中，请使用预生成的域 `HomeAutomation` 来创建 LUIS 应用，以便打开和关闭灯和设备。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>创建新应用
可在“我的应用”中创建和管理应用程序。

1. 在“我的应用”列表中，选择“+ 新建对话应用”然后在选项列表中，再次选择“+ 新建对话应用”。

1. 在对话框中，将应用程序命名为 `Home Automation`。
1. 选择“英语”作为区域性。
1. 输入可选说明。
1. 如果尚未创建预测资源，请不要选择该资源。 若要使用应用的预测终结点（过渡或生产），需要分配预测资源。
1. 选择“完成”。

    LUIS 创建应用程序。

    ![在对话框中，将应用程序命名为“家庭自动化”](./media/create-new-app-details.png)

    >[!NOTE]
    >创建应用程序后将无法更改区域性。

## <a name="add-prebuilt-domain"></a>添加预生成域

1. 在左侧导航栏中选择“预生成域”。
1. 搜索“HomeAutomation”。
1. 在 HomeAutomation 卡上选择“添加域”。

    ![选择“预生成域”，然后搜索“HomeAutomation”。 在 HomeAutomation 卡上选择“添加域”。](media/luis-quickstart-new-app/home-automation.png)

    成功添加域以后，预生成域框会显示“删除域”按钮。

## <a name="intents-and-entities"></a>意向和实体

1. 选择“意向”以查看 HomeAutomation 域意向。 预生成的域意向具有示例言语。

    ![HomeAutomation 意向列表的屏幕截图](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation 意向列表的屏幕截图")

    > [!NOTE]
    > “无”是由所有 LUIS 应用提供的意向。 可以使用它来处理与应用提供的功能无法对应的话语。

1. 选择 **HomeAutomation.TurnOff** 意向。 此意向包含一系列使用实体标记的示例言语。

    > [!div class="mx-imgBorder"]
    > [![HomeAutomation.TurnOff 意向的屏幕截图](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 意向的屏幕截图")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>测试应用
训练完应用以后，即可测试它。

1. 在右上方导航中选择“测试”。

1. 在“交互式测试”窗格中键入 `Turn off the lights` 之类的测试言语，然后按 Enter。

    ```
    Turn off the lights
    ```

    在以下示例中，`Turn off the lights` 被正确标识为“HomeAutomation.TurnOff”的评分最高的意向。

    ![“测试”面板的屏幕截图，其中突出显示了话语](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. 选择“检查”以查看有关预测的详细信息。

    > [!div class="mx-imgBorder"]
    > ![包含检查信息的测试面板的屏幕截图](media/luis-quickstart-new-app/test.png)

1. 关闭“测试”窗格。

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>查询 V3 API 预测终结点

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. 对于查询字符串，在浏览器地址栏中确保以下名称和值栏在 URL 中。 如果它们不在查询字符串中，请添加它们：

    |名称/值对|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. 在浏览器地址栏中，转到 URL 末尾，对于查询，输入 `turn off the living room light` 值，然后按 Enter。

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
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

    详细了解 [V3 预测终结点](luis-migration-api-v3.md)。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

可以从代码调用终结点：

> [!div class="nextstepaction"]
> [使用代码调用 LUIS 终结点](luis-get-started-cs-get-intent.md)
