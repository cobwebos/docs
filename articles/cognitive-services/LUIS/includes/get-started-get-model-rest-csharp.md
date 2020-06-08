---
title: 使用 C# 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/03/2020
ms.author: diberry
ms.openlocfilehash: 3b79785da683956bd9c698e8994168e70f77ad6c
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416344"
---
[参考文档](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)

## <a name="prerequisites"></a>先决条件

* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>创建披萨应用

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>以编程方式更改模型

1. 使用项目和名为 `csharp-model-with-rest` 的文件夹创建一个面向 C# 语言的新控制台应用程序。

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. 更改为创建的 `csharp-model-with-rest` 目录，并使用以下命令安装所需的依赖项：

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. 将 Program.cs 改写为以下代码：

    [!code-csharp[Code snippet](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)]

1. 将以 `YOUR-` 开头的值替换为你自己的值。

    |信息|目的|
    |--|--|
    |`YOUR-APP-ID`| LUIS 应用 ID。 |
    |`YOUR-AUTHORING-KEY`|32 字符创作密钥。|
    |`YOUR-AUTHORING-ENDPOINT`| 创作 URL 终结点。 例如，`https://replace-with-your-resource-name.api.cognitive.microsoft.com/`。 在创建资源时设置资源名称。|

    分配的密钥和资源可以在 LUIS 门户的“Azure 资源”页上的“管理”部分中看到。 应用 ID 可以在“应用程序设置”页的同一“管理”部分中找到。

1. 生成控制台应用程序。

    ```console
    dotnet build
    ```

1. 运行控制台应用程序。

    ```console
    dotnet run
    ```

1. 查看创作响应：

    ```console
    Added utterances.
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
    Sent training request.
    {
        "statusId": 9,
        "status": "Queued"
    }
    Requested training status.
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

完成本快速入门后，请从文件系统中删除项目文件夹。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的最佳实践](../luis-concept-best-practices.md)
