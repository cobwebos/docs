---
title: 快速入门：创建应用 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用预生成的域 `HomeAutomation` 创建 LUIS 应用，以便打开和关闭灯和设备。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703241"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>快速入门：使用预构建的家庭自动化应用

在本快速入门中，请使用预生成的域 `HomeAutomation` 来创建 LUIS 应用，以便打开和关闭灯和设备。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。

## <a name="prerequisites"></a>先决条件

本文需要一个在 [https://www.luis.ai](https://www.luis.ai) 的 LUIS 门户中创建的免费 LUIS 帐户。 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>创建新应用
可在“我的应用”中创建和管理应用程序  。 

1. 选择“创建新应用”。 

    [![应用列表的屏幕截图](media/luis-quickstart-new-app/app-list.png "Screenshot of app list")](media/luis-quickstart-new-app/app-list.png)

1. 在对话框中，将应用程序命名为“家庭自动化”。

    [![“创建新应用”弹出对话框的屏幕截图](media/luis-quickstart-new-app/create-new-app-dialog.png "Screenshot of Create new app pop-up dialog")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. 选择应用程序区域性。 对于此家庭自动化应用，请选择“中文”。 然后选择“完成”  。 LUIS 会创建家庭自动化应用。 

    >[!NOTE]
    >创建应用程序后将无法更改区域性。 

## <a name="add-prebuilt-domain"></a>添加预生成域

在左侧导航窗格中选择“预生成的域”。  然后搜索“家庭”。 选择“添加域”。 

[![在预构建域菜单中调出的家庭自动化域的屏幕截图](media/luis-quickstart-new-app/home-automation.png "Screenshot of Home Automation domain called out in prebuilt domain menu")](media/luis-quickstart-new-app/home-automation.png)

成功添加域以后，预生成域框会显示“删除域”按钮。 

[![带删除按钮的家庭自动化域的屏幕截图](media/luis-quickstart-new-app/remove-domain.png "Screenshot of Home Automation domain with remove button")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>意向和实体

在左侧导航窗格中选择“意向”，以便查看 HomeAutomation 域意向。  每个意向都有示例话语。

![HomeAutomation 意向列表的屏幕截图](media/luis-quickstart-new-app/home-automation-intents.png "Screenshot of HomeAutomation intents list")]

> [!NOTE]
>  “无”是由所有 LUIS 应用提供的意向。 可以使用它来处理与应用提供的功能无法对应的话语。 

选择 **HomeAutomation.TurnOff** 意向。 可以看到，此意向包含一系列使用实体标记的话语。

[![HomeAutomation.TurnOff 意向的屏幕截图](media/luis-quickstart-new-app/home-automation-turnoff.png "Screenshot of HomeAutomation.TurnOff intent")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>测试应用
训练完应用以后，即可测试它。 选择顶部导航栏中的“测试”  。 在“交互式测试”窗格中键入“关灯”之类的测试话语，然后按 Enter。 

```
Turn off the lights
```

检查评分最高的意向是否对应于每个测试话语的预期意向。

在以下示例中，`Turn off the lights` 被正确标识为“HomeAutomation.TurnOff”  的评分最高的意向。

[![突出显示了话语的“测试”面板的屏幕截图](media/luis-quickstart-new-app/test.png "Screenshot of Test panel with utterance highlighted")](media/luis-quickstart-new-app/test.png)


选择“检查”  以查看有关预测的详细信息。

![“测试”面板的屏幕截图，其中突出显示了话语](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

再次选择“测试”  ，折叠测试窗格。 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>查询 V2 API 预测终结点

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. 将光标定位到地址中 URL 的末尾并输入 `turn off the living room light`，然后按 Enter。 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 预测终结点](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    浏览器会显示 HTTP 终结点的 JSON 响应的 V2 API 版本  。

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[V3 预测终结点](#tab/V3)

    对于 [V3 API 查询](luis-migration-api-v3.md)，请在浏览器中更改 GET 方法 HTTPS 请求，并将尖括号中的值更改为自己的值。     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    详细了解 [V3 预测终结点](luis-migration-api-v3.md)。
    
    * * * 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

可以从代码调用终结点：

> [!div class="nextstepaction"]
> [使用代码调用 LUIS 终结点](luis-get-started-cs-get-intent.md)
