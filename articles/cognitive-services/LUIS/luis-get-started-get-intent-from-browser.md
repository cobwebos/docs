---
title: 快速入门：使用浏览器获取意向 - LUIS
titleSuffix: Azure Cognitive Services
description: 本快速入门在浏览器中使用可用的公共 LUIS 应用从会话文本中确定用户的意向。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703599"
---
# <a name="quickstart-get-intent-with-a-browser"></a>快速入门：使用浏览器获取意向

若要了解 LUIS 预测终结点返回的内容，请在 Web 浏览器中查看预测结果。 

## <a name="prerequisites"></a>先决条件

若要查询公共应用，需要：

* 自己的语言理解 (LUIS) 密钥。 如果你没有用于创建密钥的订阅，可以注册一个[免费帐户](https://azure.microsoft.com/free/)。
* 公共应用的 ID：`df67dcdb-c37d-46af-88e1-8b97951ca1c2`。 

## <a name="use-the-browser-to-see-predictions"></a>使用浏览器查看预测

1. 打开 Web 浏览器。 
1. 使用以下完整 URL（请将 `{your-key}` 替换为自己的 LUIS 密钥）。 请求为 GET 请求，并包含授权和 LUIS 密钥作为查询字符串参数。

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 预测终结点请求](#tab/V2)
    
    **GET** 终结点请求的 V2 URL 格式为：
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 预测终结点请求](#tab/V3)
    
    
    **GET** 终结点（按槽）请求的 V3 URL 格式为：
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. 将该 URL 粘贴到浏览器窗口中，然后按 Enter。 浏览器显示的 JSON 结果指示 LUIS 将 `HomeAutomation.TurnOn` 意向检测为首要意向，并检测到值为 `on` 的 `HomeAutomation.Operation` 实体。

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 预测终结点响应](#tab/V2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 预测终结点响应](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * *

1. 若要查看所有意向，请添加相应的查询字符串参数。 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 预测终结点](#tab/V2)

    将 `verbose=true` 添加到查询字符串末尾可**显示所有意向**：

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpointtabv3"></a>[V3 预测终结点](#tab/V3)

    将 `show-all-intents=true` 添加到查询字符串末尾可**显示所有意向**：

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * * 

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

> [!div class="nextstepaction"]
> [在 LUIS 门户中创建应用](get-started-portal-build-app.md)