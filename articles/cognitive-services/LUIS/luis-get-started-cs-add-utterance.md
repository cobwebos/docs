---
title: 教程：了解如何使用 C# 将陈述添加到 LUIS 应用 | Microsoft Docs
description: 本教程介绍如何使用 C# 调用 LUIS 应用。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264237"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>教程：使用 C# 将陈述添加到 LUIS 应用 
在本教程中，我们将编写一个程序，以使用 C# 中的创作 API 将陈述添加到意向。

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建 Visual Studio 控制台项目 
> * 添加方法以调用 LUIS API 来添加陈述和训练应用
> * 添加包含 BookFlight 意向的示例陈述的 JSON 文件
> * 运行控制台并查看陈述的训练状态

有关详细信息，请参阅[将示例陈述添加到意向](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)、[训练](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技术文档。

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="prerequisites"></a>先决条件

* 最新的 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)。 
* LUIS **[创作密钥](luis-concept-keys.md#authoring-key)**。 可以在 [LUIS](luis-reference-regions.md) 网站中的“帐户设置”下找到此密钥。
* 现有的 LUIS [**应用程序 ID**](./luis-get-started-create-app.md)。 应用程序仪表板中显示了应用程序 ID。 在运行此代码之前，包含 `utterances.json` 文件中所用的意向和实体的 LUIS 应用程序必须存在。 本文中的代码不会创建意向和实体。 它会添加现有意向和实体的陈述。 
* 接收陈述的应用程序中的**版本 ID**。 默认 ID 为“0.1”
* 在 VSCode 中创建名为 `add-utterances.cs` 的文件项目。

> [!NOTE] 
> [**LUIS-Samples** Github 存储库](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/)中提供了完整的 C# 解决方案，包括 `utterances.json` 示例文件。

## <a name="create-the-project-in-visual-studio"></a>在 Visual Studio 中创建项目

在 Visual Studio 中，使用 .Net Framework 创建新的 **Windows 经典桌面控制台**应用。 

![Visual Studio 项目类型](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>添加 System.Web 依赖项

在 Visual Studio 项目中，项目需要 **System.Web**。 在解决方案资源管理器中，右键单击“引用”并选择“添加引用”。

![添加 System.web 引用](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>编写 C# 代码
**Program.cs** 文件应为：

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

添加 System.IO 和 System.Net.Http 依赖项。

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


将 LUIS ID 和字符串添加到 **Program** 类。

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

添加 JsonPrettyPrint 方法。

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

添加用于创建陈述或开始训练的 GET 请求。 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


添加用于创建陈述或开始训练的 POST 请求。 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

添加 `AddUtterances` 函数。

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


添加 `Train` 函数。 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

添加 `Status` 函数。

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

若要管理参数，请添加 main 代码。

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>指定要添加的陈述
创建并编辑文件 `utterances.json`，以指定要添加到 LUIS 应用的**陈述数组**。 意向和实体**必须**已事先包含在 LUIS 应用中。

> [!NOTE]
> 在运行 `add-utterances.cs` 中的代码之前，包含 `utterances.json` 文件中所用的意向和实体的 LUIS 应用程序必须存在。 本文中的代码不会创建意向和实体。 它只会添加现有意向和实体的陈述。

`text` 字段包含陈述的文本。 `intentName` 字段必须对应于 LUIS 应用中的意向名称。 `entityLabels` 字段是必填的。 如果不想要标记任何实体，请提供空列表，如以下示例中所示：

如果 entityLabels 列表不为空，则 `startCharIndex` 和 `endCharIndex` 需要标记 `entityName` 字段中引用的实体。 这两个索引是从零开始的计数，这意味着，第一个示例中的 6 引用 Seattle 中的“S”，大写的 S 前面没有空格。

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="mark-the-json-file-as-content"></a>将 JSON 文件标记为内容
在解决方案资源管理器中，右键单击 `utterances.json` 并选择“属性”。 在属性窗口中，标记 `Content` 的“生成操作”，并标记 `Copy Always` 的“复制到输出目录”。  

![将 JSON 文件标记为内容](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>从命令行添加陈述

在 /bin/Debug 目录中，使用 C# 从命令行生成并运行应用程序。 确保 utterances.json 文件也在此目录中。

仅结合 utterance.json 作为参数调用 add-utterances.cs 会添加 LUIS，但不会基于新陈述训练 LUIS。
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

此命令行显示调用“添加陈述”API 的结果。 添加的陈述的 `response` 字段采用此格式。 `hasError` 为 false，表示已添加陈述。  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>从命令行添加陈述并训练
结合 `-train` 参数调用 add-utterance，以发送训练请求。 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> 重复的陈述不会再次添加，但不会导致错误。 `response` 包含原始陈述的 ID。

以下 JSON 显示了训练请求成功的结果：

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

将训练请求排队后，可能需要花费片刻时间来完成训练。

## <a name="get-training-status-from-the-command-line"></a>从命令行获取训练状态
结合 `-status` 参数调用应用，以检查训练状态，并显示状态详细信息。

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>清理资源
完成本教程后，如果不再需要 Visual Studio 和控制台应用程序，请将其删除。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [以编程方式生成 LUIS 应用](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
