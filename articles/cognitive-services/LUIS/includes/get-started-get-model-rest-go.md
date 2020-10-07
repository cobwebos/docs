---
title: 使用 Go 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: 0db01cbd281b2060c1e08e6c44202624faefc5d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91534547"
---
[参考文档](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/LUIS/go-rest-model/model.go)

## <a name="prerequisites"></a>先决条件

* [Go](https://golang.org/) 编程语言
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>创建披萨应用

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>以编程方式更改模型

1. 创建名为 `predict.go` 的新文件。 添加以下代码：

    [!code-go[Code snippet](~/cognitive-services-quickstart-code/go/LUIS/go-rest-model/model.go)]

1. 将以 `YOUR-` 开头的值替换为你自己的值。

    |信息|目的|
    |--|--|
    |`YOUR-APP-ID`| LUIS 应用 ID。 |
    |`YOUR-AUTHORING-KEY`|32 字符创作密钥。|
    |`YOUR-AUTHORING-ENDPOINT`| 创作 URL 终结点。 例如，`https://replace-with-your-resource-name.api.cognitive.microsoft.com/`。 在创建资源时设置资源名称。|

    分配的密钥和资源可以在 LUIS 门户的“Azure 资源”页上的“管理”部分中看到。 应用 ID 可以在“应用程序设置”页的同一“管理”部分中找到。

1. 在创建该文件的同一目录中，在命令提示符下输入以下命令来编译 Go 文件：

    ```console
    go build model.go
    ```

1. 通过在命令提示符下输入以下文本从命令行运行 Go 应用程序：

    ```console
    go run model.go
    ```

1. 查看创作响应：

    ```console
    add example utterances requested
    body
    [{'text': 'order a pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 12}]}, {'text': 'order a large pepperoni pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'PizzaType', 'startCharIndex': 14, 'endCharIndex': 28}, {'entityName': 'Size', 'startCharIndex': 8, 'endCharIndex': 12}]}, {'text': 'I want two large pepperoni pizzas on thin crust', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'PizzaType', 'startCharIndex': 17, 'endCharIndex': 32}, {'entityName': 'Size', 'startCharIndex': 11, 'endCharIndex': 15}, {'entityName': 'Quantity', 'startCharIndex': 7, 'endCharIndex': 9}, {'entityName': 'Crust', 'startCharIndex': 37, 'endCharIndex': 46}]}]
        201
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    training selected
    body

        202
    {"statusId":9,"status":"Queued"}
    training status selected
    body

        200
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":9,"status":"Queued","exampleCount":0}}]
    ```

    下面是为提高可读性而进行了格式设置的输出：

    ```json
    add example utterances requested
    body
    [
      {
        'text': 'order a pizza',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 6,
            'endCharIndex': 12
          }
        ]
      },
      {
        'text': 'order a large pepperoni pizza',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 6,
            'endCharIndex': 28
          },
          {
            'entityName': 'FullPizzaWithModifiers',
            'startCharIndex': 6,
            'endCharIndex': 28
          },
          {
            'entityName': 'PizzaType',
            'startCharIndex': 14,
            'endCharIndex': 28
          },
          {
            'entityName': 'Size',
            'startCharIndex': 8,
            'endCharIndex': 12
          }
        ]
      },
      {
        'text': 'I want two large pepperoni pizzas on thin crust',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 7,
            'endCharIndex': 46
          },
          {
            'entityName': 'FullPizzaWithModifiers',
            'startCharIndex': 7,
            'endCharIndex': 46
          },
          {
            'entityName': 'PizzaType',
            'startCharIndex': 17,
            'endCharIndex': 32
          },
          {
            'entityName': 'Size',
            'startCharIndex': 11,
            'endCharIndex': 15
          },
          {
            'entityName': 'Quantity',
            'startCharIndex': 7,
            'endCharIndex': 9
          },
          {
            'entityName': 'Crust',
            'startCharIndex': 37,
            'endCharIndex': 46
          }
        ]
      }
    ]

        201
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    training selected
    body

        202
    {
      "statusId": 9,
      "status": "Queued"
    }
    training status selected
    body

        200
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除该文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的最佳实践](../luis-concept-best-practices.md)