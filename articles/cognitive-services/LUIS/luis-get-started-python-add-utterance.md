---
title: 教程：了解如何使用 Python 将陈述添加到 LUIS 应用 | Microsoft Docs
description: 本教程介绍如何使用 Python 调用 LUIS 应用。
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 3e1ca2ea874b6b39ac8a1b1eaa94fe9ff1894ea3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264295"
---
# <a name="tutorial-add-utterances-to-app-using-python"></a>教程：使用 Python 将陈述添加到应用
在本教程中，我们将编写一个程序，以使用 Python 中的创作 API 将陈述添加到意向。

<!-- green checkmark -->
> [!div class="checklist"]
> * 创建 Visual Studio 控制台项目 
> * 添加方法以调用 LUIS API 来添加陈述和训练应用
> * 添加包含 BookFlight 意向的示例陈述的 JSON 文件
> * 运行控制台并查看陈述的训练状态

有关详细信息，请参阅[将示例陈述添加到意向](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)、[训练](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)和[训练状态](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API 的技术文档。

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="prerequisites"></a>先决条件

* [Python 3.6](https://www.python.org/downloads/) 或更高版本。
* **[建议]** 用于 IntelliSense 和调试的 [Visual Studio Code](https://code.visualstudio.com/)。
* LUIS **[创作密钥](luis-concept-keys.md#authoring-key)**。 可以在 [LUIS](luis-reference-regions.md) 网站中的“帐户设置”下找到此密钥。
* 现有的 LUIS [**应用程序 ID**](./luis-get-started-create-app.md)。 应用程序仪表板中显示了应用程序 ID。 在运行 `add-utterances.js` 中的代码之前，包含 `utterances.json` 文件中所用的意向和实体的 LUIS 应用程序必须存在。 本文中的代码不会创建意向和实体。 它只会添加现有意向和实体的陈述。 
* 接收陈述的应用程序中的**版本 ID**。 默认 ID 为“0.1”
* 在 VSCode 中创建名为 `add-utterances-3-6.py` 的文件项目。

> [!NOTE] 
> [**LUIS-Samples** Github 存储库](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/python)中提供了完整的 `add-utterances-3-6.py` 文件。


## <a name="write-the-python-code"></a>编写 Python 代码

1. 复制以下代码片段：

   [!code-python[Console app code that adds an utterance Python 3.6](~/samples-luis/documentation-samples/authoring-api-samples/python/add-utterances-3-6.py)]

## <a name="specify-utterances-to-add"></a>指定要添加的陈述
创建并编辑文件 `utterances.json`，以指定要添加到 LUIS 应用的**陈述数组**。 意向和实体**必须**已事先包含在 LUIS 应用中。

> [!NOTE]
> 在运行 `add-utterances.js` 中的代码之前，包含 `utterances.json` 文件中所用的意向和实体的 LUIS 应用程序必须存在。 本文中的代码不会创建意向和实体。 它只会添加现有意向和实体的陈述。

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

## <a name="add-an-utterance-from-the-command-line"></a>从命令行添加陈述

使用 Python 3.6 在命令行中运行该应用程序。

调用不带参数的 add-utterance 会将陈述添加到应用，但不训练应用。

````
> python add-utterances-3-6.py
````

此示例创建带有 `results.json` 的文件，其中包含调用“添加陈述”API 的结果。 添加的陈述的 `response` 字段采用此格式。 `hasError` 为 false，表示已添加陈述。  

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
结合 `-train` 参数调用 add-utterance，以发送训练请求，然后请求训练状态。 训练开始后，状态会立即排队。 状态详细信息将写入某个文件。

````
> python add-utterances-3-6.py -train
````

> [!NOTE]
> 重复的陈述不会再次添加，但不会导致错误。 `response` 包含原始陈述的 ID。

结合 `-train` 参数调用示例时，会创建 `training-results.json` 文件，指出已将训练 LUIS 应用的请求成功排队。 

以下示例显示了训练请求成功的结果：
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
结合 `-status` 参数调用示例，以检查训练状态，并将状态详细信息写入某个文件。

````
> python add-utterances-3-6.py -status
````
## <a name="clean-up-resources"></a>清理资源
完成本教程后，如果不再需要 Visual Studio 和控制台应用程序，请将其删除。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [以编程方式生成 LUIS 应用](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

