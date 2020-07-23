---
title: 快速入门：使用 REST API 获取意向 - LUIS
titleSuffix: Azure Cognitive Services
description: 此 REST API 快速入门使用可用的公共 LUIS 应用从对话文本中确定用户的意向。
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: 714d76641dd22f566b2464e3470f6a3324f4d758
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207183"
---
# <a name="quickstart-use-curl-and-rest-to-get-intent"></a>快速入门：使用 cURL 和 REST 获取意向

本快速入门介绍如何使用 LUIS 应用从会话文本中确定用户的意向。 你将使用 cURL 将用户的意向作为文本发送到 Pizza 应用的 HTTP 预测终结点。 在终结点处，LUIS 应用 Pizza 应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。

[参考文档](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)

## <a name="prerequisites"></a>先决条件

* 免费的 [LUIS](https://www.luis.ai) 帐户。
* 文本编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)。
* 命令行程序 cURL。 在 macOS、大多数 Linux 分发版和 Windows 10 版本 1803 及更高版本上已安装该 cURL 程序。 若需要安装 cURL，可在 [cURL 下载页](https://curl.haxx.se/download.html)中下载 cURL。

## <a name="create-pizza-app"></a>创建 Pizza 应用

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>从预测终结点获取意向

使用 cURL 查询[预测终结点](https://aka.ms/luis-apim-v3-prediction)并获取预测结果。

1. 请将以下命令复制到自己的文本编辑器中：

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=***YOUR-PREDICTION-KEY***" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    ***

1. 将以 `***YOUR-` 开头的值替换为你自己的值。

    |信息|目的|
    |--|--|
    |`***YOUR-PREDICTION-ENDPOINT***`| 预测 URL 终结点。 位于 LUIS 门户中，你的应用的“Azure 资源”页。<br>例如，`https://westus.api.cognitive.microsoft.com/`。|
    |`***YOUR-APP-ID***`|你的应用程序 ID。 位于 LUIS 门户中，你的应用的“应用程序设置”页。
    |`***YOUR-PREDICTION-KEY***`|32 字符预测密钥。 位于 LUIS 门户中，你的应用的“Azure 资源”页。

1. 将文本复制到控制台窗口并按 Enter 执行该命令：

1. 使用以下控制台命令运行脚本：

    ```console
    python predict.py
    ```

1. 查看以 JSON 形式返回的预测响应：

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    已通过格式化提高可读性的 JSON 响应：

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
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
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
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
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加言语并进行训练](luis-get-started-rest-get-model.md)
