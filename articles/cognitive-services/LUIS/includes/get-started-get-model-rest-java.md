---
title: 使用 Java 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 33efca9ab3c24ebd0a3d1c76924f2c1f4ee89581
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534548"
---
[参考文档](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/LUIS/java-model-with-rest/Model.java)

## <a name="prerequisites"></a>先决条件

* [JDK SE](https://aka.ms/azure-jdks)（Java 开发工具包，标准版）
* [Visual Studio Code](https://code.visualstudio.com/) 或你喜欢用的 IDE

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>以编程方式更改模型

1. 创建一个新文件夹以保存 Java 项目，例如 `java-model-with-rest`。

1. 创建一个名为 `lib` 的子目录，并将以下 Java 库中的内容复制到 `lib` 子目录：

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. 创建名为 `Model.java` 的新文件。 添加以下代码：

    [!code-java[Code snippet](~/cognitive-services-quickstart-code/java/LUIS/java-model-with-rest/Model.java)]

1. 将以 `YOUR-` 开头的值替换为你自己的值。

    |信息|目的|
    |--|--|
    |`YOUR-APP-ID`| LUIS 应用 ID。 |
    |`YOUR-AUTHORING-KEY`|32 字符创作密钥。|
    |`YOUR-AUTHORING-ENDPOINT`| 创作 URL 终结点。 例如，`https://replace-with-your-resource-name.api.cognitive.microsoft.com/`。 在创建资源时设置资源名称。|

    分配的密钥和资源可以在 LUIS 门户的“Azure 资源”页上的“管理”部分中看到。 应用 ID 可以在“应用程序设置”页的同一“管理”部分中找到。

1. 在创建 `Model.java` 文件的同一目录中，在命令提示符下输入以下命令来编译 Java 文件：

    * 如果使用的是 Windows，请使用此命令：`javac -cp ";lib/*" Model.java`
    * 如果使用的是 macOS 或 Linux，请使用此命令：`javac -cp ":lib/*" Model.java`

1. 通过在命令提示符下输入以下文本从命令行运行 Java 应用程序：

    * 如果使用的是 Windows，请使用此命令：`java -cp ";lib/*" Model`
    * 如果使用的是 macOS 或 Linux，请使用此命令：`java -cp ":lib/*" Model`

1. 查看创作响应：

    ```console
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    {"statusId":9,"status":"Queued"}
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":3,"status":"InProgress","exampleCount":0,"progressSubstatus":"CollectingData"}}]
    ```

    下面是为提高可读性而进行了格式设置的输出：

    ```json
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
    {
      "statusId": 9,
      "status": "Queued"
    }
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
          "statusId": 3,
          "status": "InProgress",
          "exampleCount": 0,
          "progressSubstatus": "CollectingData"
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除项目文件夹。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的最佳实践](../luis-concept-best-practices.md)